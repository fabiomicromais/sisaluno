EDUARDA MUNIZ GUIMARÃES CARVALHO
cadaluno.php
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Document</title>
</head>
<body>
<form method="GET" action="crudaluno.php">
<label for="">nome aluno</label>
<input type="text" name="nomealuno">
<label for="">idade</label>
<input type="text" name="idade">
<input type="submit" name="cadastrar" value="cadastrar">
</form>
<button class="button"><a href="index.php">voltar</a></button>
</body>
</html>
conexao.php
<?php
//Criar as constantes com as credencias de acesso ao banco de dados
define('HOST', 'localhost');
define('USUARIO', 'root');
define('SENHA', 'root');
define('DBNAME', 'db-if');
//Criar a conexão com banco de dados usando o PDO e a porta do banco de dados
//Utilizar o Try/Catch para verificar a conexão.
try {
$conexao = new pdo('mysql:host=' . HOST . ';dbname=' .
DBNAME, USUARIO, SENHA);
} catch (PDOException $e) {
echo "Erro: Conexão com banco de dados não foi realizada com sucesso.
Erro gerado " . $e->getMessage();
}
?>
index.php
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Document</title>
</head>
<body>
<button class="button"><a href="cadaluno.php">cadastrar</a></button>
<button class="button"><a href="listaalunos.php">lista</a></button>
</body>
</html>
listaaluno.php
<?php
/*
* Melhor prática usando Prepared Statements
*
*/
require_once('conexao.php');
$retorno = $conexao->prepare('SELECT * FROM aluno');
$retorno->execute();
?>
<table>
<thead>
<tr>
<th>ID</th>
<th>NOME</th>
<th>IDADE</th>
</tr>
</thead>
<tbody>
<tr>
<?php foreach($retorno->fetchall() as $value) { ?>
<tr>
<td> <?php echo $value['id'] ?> </td>
<td> <?php echo $value['nome']?> </td>
<td> <?php echo $value['idade']?> </td>
<td>
<form method="POST" action="altaluno.php">
<input name="id" type="hidden" value="<?php echo $value['id'];?>"/>
<button name="alterar" type="submit">Alterar</button>
</form>
</td>
<td>
<form method="GET" action="crudaluno.php">
<input name="id" type="hidden" value="<?php echo $value['id'];?>"/>
<button name="excluir" type="submit">Excluir</button>
</form>
</td>
</tr>
<?php } ?>
</tr>
</tbody>
</table>
<?php
echo "<button class='button button3'><a href='index.php'>voltar</a></button>";
?>
altaluno.php
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Document</title>
</head>
<body>
<?php
require_once('conexao.php');
$id = $_POST['id'];
##sql para selecionar apens um aluno
$sql = "SELECT * FROM aluno where id= :id";
# junta o sql a conexao do banco
$retorno = $conexao->prepare($sql);
##diz o paramentro e o tipo do paramentros
$retorno->bindParam(':id',$id, PDO::PARAM_INT);
#executa a estrutura no banco
$retorno->execute();
#transforma o retorno em array
$array_retorno=$retorno->fetch();
##armazena retorno em variaveis
$nome = $array_retorno['nome'];
$idade = $array_retorno['idade'];
?>
<form method="POST" action="crudaluno.php">
<input type="text" name="nome" id="" value=<?php echo $nome?> >
<input type="text" name="idade" id="" value=<?php echo $idade ?> >
<input type="hidden" name="id" id="" value=<?php echo $id ?> >
<input type="submit" name="update" value="Alterar">
</form>
</body>
</html>
crudaluno.php
<?php
##permite acesso as variaves dentro do aquivo conexao
require_once('conexao.php');
##cadastrar
if(isset($_GET['cadastrar'])){
##dados recebidos pelo metodo GET
$nome = $_GET["nomealuno"];
$idade = $_GET["idade"];
##codigo SQL
$sql = "INSERT INTO aluno(nome,idade)
VALUES('$nome','$idade')";
##junta o codigo sql a conexao do banco
$sqlcombanco = $conexao->prepare($sql);
##executa o sql no banco de dados
if($sqlcombanco->execute())
{
echo " <strong>OK!</strong> o aluno
$nome foi Incluido com sucesso!!!";
echo " <button class='button'><a href='index.html'>voltar</a></button>";
}
}
#######alterar
if(isset($_POST['update'])){
##dados recebidos pelo metodo POST
$nome = $_POST["nome"];
$idade = $_POST["idade"];
$id = $_POST["id"];
##codigo sql
$sql = "UPDATE aluno SET nome= :nome, idade= :idade WHERE id= :id ";
##junta o codigo sql a conexao do banco
$stmt = $conexao->prepare($sql);
##diz o paramentro e o tipo do paramentros
$stmt->bindParam(':id',$id, PDO::PARAM_INT);
$stmt->bindParam(':nome',$nome, PDO::PARAM_STR);
$stmt->bindParam(':idade',$idade, PDO::PARAM_INT);
$stmt->execute();
if($stmt->execute())
{
echo " <strong>OK!</strong> o aluno
$nome foi Alterado com sucesso!!!";
echo " <button class='button'><a href='index.php'>voltar</a></button>";
}
}
##Excluir
if(isset($_GET['excluir'])){
$id = $_GET['id'];
$sql ="DELETE FROM `aluno` WHERE id={$id}";
$conexao->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
$stmt = $conexao->prepare($sql);
$stmt->execute();
if($stmt->execute())
{
echo " <strong>OK!</strong> o aluno
$id foi excluido!!!";
echo " <button class='button'><a href='listaalunos.php'>voltar</a></button>";
}
}
?>
