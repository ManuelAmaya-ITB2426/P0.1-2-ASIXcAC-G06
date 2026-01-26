# DOCUMENTACIÓN - SCRIPTS PHP

## Objetivo
**Frontend principal Extagram (extagram.php) :**
- Formulario upload texto+imagen
- Mostrar posts ordenados (ID DESC)
- Soporte híbrido: filesystem /storage/ + BLOB fallback
- Alertas usuario (?error=sin_texto)
- Interfaz Instagram-like responsive

**Procesador uploads inteligente (upload.php):**
- Validación texto obligatorio
- Doble storage: Filesystem → BLOB fallback
- Manejo errores silencioso
- Redirección automática
- Alta disponibilidad (no falla si disco lleno)

**Objetivo conjunto:** MVP social network funcional con resiliencia storage.

## Extahgram.php y Upload.php
Aqui mostare la configuracion pricipal que se nos presento y el resultado del como fuimos modificando segun nuestras necesidades.

**extagram.php:**
- Codigo inicial:
  ```sql
  <!DOCTYPE html>
  <link rel="stylesheet" href="https://static.extagram.itb/style.css">
 
  <form method="POST" enctype="multipart/form-data" action="upload.php">
	  <input type="text" name="post" placeholder="Write something...">
	  <input id="file" type="file" name="photo" onchange="document.getElementById('preview').src=window.URL.createObjectURL(event.target.files[0])">
	  <label for="file">
                  	 <img id="preview" src="https://static.extagram.itb/preview.svg">
	  </label>
	  <input type="submit" value=”Publish”>
  </form>
  <?php
  $db = new mysqli("db.extagram.itb", "extagram_admin", "pass123", "extagram_db");
 
  foreach ($db->query("SELECT * FROM posts") as $fila) {
                  	echo "<div class='post'>";
                  	echo "<p>".$fila['post']."</p>";
                  	if (!empty($fila['photourl'])) {
	              	    echo "<img src='https://storage.extagram.itb/".$fila['photourl']."'>";
                  	}
                  	echo "</div>";
  }
  ?>
  ```
  
- Codigo final:
  ```sql
  <!DOCTYPE html>
  <html lang="es">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Extagram</title>
      <link rel="stylesheet" href="/static/style.css">
  </head>
  <body>

  <?php if (isset($_GET['error']) && $_GET['error'] == 'sin_texto'): ?>
  <div style="background:#ff4444; color:white; padding:15px; text-align:center; margin:20px auto; max-width:500px; border-radius:8px;">
      ⚠️ Debes escribir algo antes de publicar
  </div>
  <?php endif; ?>

  <form method="POST" enctype="multipart/form-data" action="upload.php">
      <input type="text" name="post" placeholder="Write something..." required>
      <input id="file" type="file" name="photo" accept="image/*" onchange="document.getElementById('preview').src=window.URL.createObjectURL(event.target.files[0])">
      <label for="file">
          <img id="preview" src="/static/preview.svg">
      </label>
      <input type="submit" value="Publish">
  </form>

  <?php
  $db = new mysqli("localhost", "extagram_admin", "pass123", "extagram_db");

  if ($db->connect_error) {
      echo "<p style='text-align:center;color:#999'>Database not available yet</p>";
  } else {
      $result = $db->query("SELECT * FROM posts ORDER BY id DESC");
      if ($result && $result->num_rows > 0) {
          foreach ($result as $fila) {
              echo "<div class='post'>";
              echo "<p>" . htmlspecialchars($fila['post']) . "</p>";
            
              // Mostrar imagen: filesystem O BLOB
              if (!empty($fila['photourl']) && file_exists('/var/www/extagram/uploads/' . $fila['photourl'])) {
                  echo "<img src='/storage/" . htmlspecialchars($fila['photourl']) . "' style='max-width:100%; height:auto; border-radius:8px;'>";
              } elseif (!empty($fila['filedata'])) {
                  $imgData = base64_encode($fila['filedata']);
                  echo "<img src='data:" . htmlspecialchars($fila['mimetype']) . ";base64," . $imgData . "' style='max-width:100%; height:auto; border-radius:8px;'>";
              }
              
              echo "</div>";
          }
      } else {
          echo "<p style='text-align:center;color:#999'>No posts yet</p>";
      }
      $db->close();
  }
  ?>
  </body>
  </html>
  ```

**upload.php:**
- Codigo inicial:
  ```sql
  <?php
  if (!empty($_POST["post"])) {
 
	  $photoid;
	  if(!empty($_FILES['photo']['name'])){
    	$photoid = uniqid();
          move_uploaded_file($_FILES['photo']['tmp_name'], 'uploads/' . $photoid);
	  }
	  $db = new mysqli("db.extagram.itb", "extagram_admin", "pass123", "extagram_db");
	  $stmt = $db->prepare("INSERT INTO posts VALUES(?,?)");
	  $stmt->bind_param("ss", $_POST["post"], $photoid);
	  $stmt->execute();
	  $stmt->close();
  }
 
  header("location: /");
  ?>
  ```

- Codigo final:
  ```sql
  <?php
  error_reporting(E_ALL);
  ini_set('display_errors', 1);
  file_put_contents('/tmp/upload_debug.log', "\n=== " . date('Y-m-d H:i:s') . " ===\n", FILE_APPEND);

  if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    
      if (empty($_POST['post'])) {
          file_put_contents('/tmp/upload_debug.log', "ERROR: Falta texto\n", FILE_APPEND);
          header('Location: /src/extagram.php?error=sin_texto');
          exit;
      }
    
      file_put_contents('/tmp/upload_debug.log', "POST: " . $_POST['post'] . "\n", FILE_APPEND);
    
      // Procesar foto
      $photoid = '';
      $filemane = '';
      $mimetype = '';
      $filedata = null;
    
      if (!empty($_FILES['photo']['name'])) {
          $ext = pathinfo($_FILES['photo']['name'], PATHINFO_EXTENSION);
          $photoid = uniqid() . '.' . $ext;
          $uploadDir = '/var/www/extagram/uploads/';
        
          // INTENTAR filesystem
          if (!is_dir($uploadDir)) {
              mkdir($uploadDir, 0777, true);
          }
        
          if (move_uploaded_file($_FILES['photo']['tmp_name'], $uploadDir . $photoid)) {
              file_put_contents('/tmp/upload_debug.log', "✅ Filesystem OK: $photoid\n", FILE_APPEND);
          } else {
              // FALLBACK BLOB
              $filemane = $_FILES['photo']['name'];
              $mimetype = $_FILES['photo']['type'];
              $filedata = file_get_contents($_FILES['photo']['tmp_name']);
              $photoid = '';
              file_put_contents('/tmp/upload_debug.log', "⚠️ BLOB fallback: $filemane\n", FILE_APPEND);
          }
      }
    
      // Guardar en DB
      $db = new mysqli("localhost", "extagram_admin", "pass123", "extagram_db");
    
      if (!$db->connect_error) {
          if (!empty($filedata)) {
              // BLOB
              $stmt = $db->prepare("INSERT INTO posts (post, photourl, filemane, mimetype, filedata) VALUES (?, ?, ?, ?, ?)");
              $stmt->bind_param("sssss", $_POST['post'], $photoid, $filemane, $mimetype, $filedata);
          } else {
              // Filesystem
              $stmt = $db->prepare("INSERT INTO posts (post, photourl) VALUES (?, ?)");
              $stmt->bind_param("ss", $_POST['post'], $photoid);
          }
          $stmt->execute();
          file_put_contents('/tmp/upload_debug.log', "✅ INSERT OK\n", FILE_APPEND);
          $stmt->close();
          $db->close();
      }
    
      header('Location: /src/extagram.php');
      exit;
  }

  header('Location: /src/extagram.php');
  exit;
  ?>
  ```

