# ONLINE-FOOD-ORDERING-SYSTEM
7.1 Homepage
<?php
include 'components/connect.php';
session_start();
if(isset($_SESSION['user_id'])){
$user_id = $_SESSION['user_id'];
}else{
$user_id = '';
header('location:home.php');
};
if(isset($_POST['submit'])){ $name = $_POST['name'];
$name = filter_var($name, FILTER_SANITIZE_STRING);
$number = $_POST['number'];
$number = filter_var($number, FILTER_SANITIZE_STRING);
$email = $_POST['email'];
$email = filter_var($email, FILTER_SANITIZE_STRING);
$method = $_POST['method'];
$method = filter_var($method, FILTER_SANITIZE_STRING);
$address = $_POST['address'];
$address = filter_var($address, FILTER_SANITIZE_STRING);
$total_products = $_POST['total_products'];
$total_price = $_POST['total_price'];
$check_cart = $conn->prepare("SELECT * FROM `cart` WHERE user_id = ?");
$check_cart->execute([$user_id]);
if($check_cart->rowCount() > 0){
if($address == ''){
$message[] = 'please add your address!';
}else{
$insert_order = $conn->prepare("INSERT INTO `orders`(user_id, name, number,
email, method, address, total_products, total_price) VALUES(?,?,?,?,?,?,?,?)");
$insert_order->execute([$user_id, $name, $number, $email, $method, $address,
$total_products, $total_price]);
$delete_cart = $conn->prepare("DELETE FROM `cart` WHERE user_id = ?");
$delete_cart->execute([$user_id]);
$message[] = 'order placed successfully!
}}else{
$message[] = 'your cart is empty';
}
}
?>
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>checkout</title>
  <!-- font awesome cdn link -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.1.1/css/
all.min.css">
<!-- custom css file link -->
<link rel="stylesheet" href="css/style.css">
</head>
<body>
<!-- header section starts -->
<?php include 'components/user_header.php'; ?>
 <!-- header section ends -->
<div class="heading">
<h3>checkout</h3>
<p><a href="home.php">home</a> <span> / checkout</span></p>
</div>
<section class="checkout">
<h1 class="title">order summary</h1>
<form action="" method="post"<div class="cart-items">
<h3>cart items</h3>
<?php
$grand_total = 0;
$cart_items[] = '';
$select_cart = $conn->prepare("SELECT * FROM `cart` WHERE user_id = ?");
$select_cart->execute([$user_id]);
if($select_cart->rowCount() > 0){
while($fetch_cart = $select_cart->fetch(PDO::FETCH_ASSOC)){
$cart_items[]=$fetch_cart['name'].'('.$fetch_cart['price'].' x '.$fetch_cart['quantity'].')
- ';
$total_products = implode($cart_items);
$grand_total += ($fetch_cart['price'] * $fetch_cart['quantity']);
?>
<p><span class="name"><?= $fetch_cart['name']; ?></span><span class="price"> <?=
₹<?= $fetch_cart['price']; ?> x <?= $fetch_cart['quantity']; ?></span></p>
<?php
}
}else{
echo '<p class="empty">your cart is empty!</p>';
}
?>
<p class="grand-total"><span class="name">grand total :</span><span class="price">
<?=₹<?= $grand_total; ?></span></p>
<a href="cart.php" class="btn">veiw cart</a>
</div>
<input type="hidden" name="total_products" value="<?= $total_products; ?>">
<input type="hidden" name="total_price" value="<?= $grand_total; ?>" value="">
<input type="hidden" name="name" value="<?= $fetch_profile['name'] ?>">
<input type="hidden" name="number" value="<?= $fetch_profile['number'] ?>">
<input type="hidden" name="email" value="<?= $fetch_profile['email'] ?>">
<input type="hidden" name="address" value="<?= $fetch_profile['address'] ?>"><div
class="user-info">
<h3>your info</h3>
<p><i class="fas fa-user"></i><span><?= $fetch_profile['name'] ?></span></p>
<p><i class="fas fa-phone"></i><span><?= $fetch_profile['number'] ?></span></p>
<p><i class="fas fa-envelope"></i><span><?= $fetch_profile['email'] ?></span></p>
<a href="update_profile.php" class="btn">update info</a>
<h3>delivery address</h3>
<p><i class="fas fa-map-marker-alt"></i><span><?php if($fetch_profile['address'] == '')
{echo 'please enter your address';}else{echo $fetch_profile['address'];} ?></span></p>
<a href="update_address.php" class="btn">update address</a>
<select name="method" class="box" required>
<option value="" disabled selected>select payment method --</option>
<option value="cash on delivery">cash on delivery</option>
<option value="credit card">credit card</option>
<option value="paytm">paytm</option>
<option value="paypal">paypal</option>
</select>
<input type="submit" value="place order" class="btn <?php if($fetch_profile['address']
==
''){echo 'disabled';} ?>" style="width:100%; background:var(--red); color:var(--white);"
name="submit">
</div>
</form>
</section>
<!-- footer section starts -->
<?php include 'components/footer.php'; ?>
<!-- footer section ends -->
<!-- custom js file link -->
<script src="js/script.js"></script>
</body>
</html>
7.2 Login page
<?php
include 'components/connect.php'; session_start();
if(isset($_SESSION['user_id'])){
$user_id = $_SESSION['user_id'];
}else{
$user_id = '';
};
if(isset($_POST['submit'])){
$email = $_POST['email'];
$email = filter_var($email, FILTER_SANITIZE_STRING);
$pass = sha1($_POST['pass']);
$pass = filter_var($pass, FILTER_SANITIZE_STRING);$select_user = $conn-
>prepare("SELECT * FROM `users` WHERE email = ? AND password = ?");
$select_user->execute([$email, $pass]);
$row = $select_user->fetch(PDO::FETCH_ASSOC);
if($select_user->rowCount() > 0){
$_SESSION['user_id'] = $row['id'];
header('location:home.php');
}else{
$message[] = 'incorrect username or password!';
}
}
?>
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>login</title>
<!-- font awesome cdn link -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.1.1/css/
all.min.css">
<!-- custom css file link -->
<link rel="stylesheet" href="css/style.css">
</head>
<body>
<!-- header section starts -->
<?php include 'components/user_header.php'; ?>
<!-- header section ends -->
<section class="form-container">
<form action="" method="post">
<h3>login now</h3>
<input type="email" name="email" required placeholder="enter your email" class="box"
maxlength="50" oninput="this.value = this.value.replace(/\s/g, '')"
<p>don't have an account? <a href="register.php">register now</a></p>
</form>
<center
style="margin-top:50px;"><a
class="btn
btn-primary"
href="./admin/admin_login.php">Admin Login</a><center>
</section>
<?php include 'components/footer.php'; ?>
<!-- custom js file link -->
<script src="js/script.js"></script>
</body>
</html>
7.3 Contact
<?php
include 'components/connect.php';
session_start();
if(isset($_SESSION['user_id'])){
$user_id = $_SESSION['user_id'];
style="width:300px;"
}else{
$user_id = '';
};
if(isset($_POST['send'])){ $name = $_POST['name'];
$name = filter_var($name, FILTER_SANITIZE_STRING);
$email = $_POST['email'];
$email = filter_var($email, FILTER_SANITIZE_STRING);
$number = $_POST['number'];
$number = filter_var($number, FILTER_SANITIZE_STRING);
$msg = $_POST['msg'];
$msg
=
filter_var($msg,
FILTER_SANITIZE_STRING);$select_message
=
$conn>prepare("SELECT * FROM `messages` WHERE name = ? AND email = ? AND number = ? AND message = ?");
$select_message->execute([$name,
$email,
$number,
$msg]);
if($select_message>rowCount() > 0){
$message[] = 'already sent message!';
}else{
$insert_message = $conn->prepare("INSERT INTO `messages`(user_id, name, email,
number, message) VALUES(?,?,?,?,?)");
$insert_message->execute([$user_id, $name, $email, $number, $msg]);
$message[] = 'sent message successfully!';
}}
?>
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>contact</title> <!-- font awesome cdn link -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.1.1/css/
all.min.css">
<!-- custom css file link -->
<link rel="stylesheet" href="css/style.css">
 </head>
<body>
<!-- header section starts -->
<?php include 'components/user_header.php'; ?>
<!-- header section ends -->
<div class="heading">
<h3>contact us</h3>
<p><a href="home.php">home</a> <span> / contact</span></p>
</div>
<!-- contact section starts -->
<section class="contact">
<div class="row"><div class="image">
<img src="images/contact-img.svg" alt="">
</div><form action="" method="post">
<h3>tell us something!</h3>
<input type="text" name="name" maxlength="50" class="box" placeholder="enter your
name" required>
<input type="number" name="number" min="0" max="9999999999" class="box"
placeholder="enter your number" required maxlength="10">
<input type="email" name="email" maxlength="50" class="box" placeholder="enter
your email" required>
<textarea name="msg" class="box" required placeholder="enter your message"
maxlength="500" cols="30" rows="10"></textarea>
<input type="submit" value="send message" name="send" class="btn">
</div></section>
<!-- contact section ends --> <!-- footer section starts -->
<?php include 'components/footer.php'; ?>
<!-- footer section ends -->
<!-- custom js file link -->
<script src="js/script.js"></script>
</body>
</html>
