<?php
function getLevelByXp($xp){
$listlevel=array(0,29,60,100,200,350,550,800,1113,1504,1993,2604,3368,4323,5517,7010,8876,11209,13659,16232,18934,21771,24750,27878,31162,34610,38230,42031,46022,50213,54614,59235,64087,69182,74532,80150,86049,92243,98747,105576,112746,120275,128180,136480,145195,154346,163955,174044,184637,195760,207439,219702,232578,246098,260294,275200,290851,307285,324541,342660,361685,381661,402636,424660,447785,472066,497561,524331,552440,581954,612944,645484,679651,715526,756782,804226,858787,921532,993689,1076670,1172098,1281840,1408043,1553176,1720079,1912017,2132746,2386584,2678498,3014199,3400255,3844219,4354778,4941921,5617135,6393631,7286601,8313517,9494470,2016089205);
$j=count($listlevel);
for($i=0;$i<$j;$i++){
if($listlevel[$i] > $xp) break;
}
return $i;
}
$str = '<html><head><title>Social Empire X</title></head><body>
<link href="http://fonts.googleapis.com/css?family=Averia+Sans+Libre" rel="stylesheet"type="text/css">
<link href="http://fonts.googleapis.com/css?family=Orbitron:700" rel="stylesheet"type="text/css">
<link rel="stylesheet" type="text/css" href="Style.css" />
<font style="left:5px; size:10px; position:fixed; bottom:5px;"><b>By:</b><a href="http://www.facebook.com/takeshi.Reba">Slacker Games</a></font>
<center>My Facebook <a href="http://www.facebook.com/takeshi.Reba" target="_blank">Click Here</a><BR/>
<form action="social empires.php" method="post">
ID FB:<br/>
<input name="fbid" value="100003713273590"/><br/>
cash:<br/>
<input name="cash" value="999999999"/><br/>
level:<br/>
<input name="level" value="999999999"/><br/>
xp:<br/>
<input name="xp" value="999999999"/><br/>
gold:<br/>
<input name="gold" value="999999999"/><br/>
stone:<br/>
<input name="stone" value="999999999"/><br/>
wood:<br/>
<input name="wood" value="999999999"/><br/>
food:<br/>
<input name="food" value="999999999"/><br/>
<input type="submit" value="Submit" />
</form>';
if(isset($_POST['fbid']) and isset($_POST['level']) and isset($_POST['xp']) and isset($_POST['cash']) and isset($_POST['gold']) and isset($_POST['stone']) and isset($_POST['wood']) and isset($_POST['food'])){
if(empty($_POST['fbid'])) die('FB ID tidak boleh kosong.');
$cid = round(rand() * 0x40000000);
$postdata=http_build_query(array('neighbors'=>"",'client_id'=>$cid));
$opts = array('http' =>
array(
'method' => 'POST',
'header' => "Content-type: application/x-www-form-urlencoded\r\n"."Content-Length: ".strlen($postdata)."\r\n",
'content' => $postdata
)
);
if(!sendPost($result,"ERROR 2000","http://dynamic01.socialpointgames.com/appsfb/socialempires/srvempires/get_player_info.php?USERID=$_POST[fbid]&spdebug=1",$opts)) die($result);
$payload = explode(';',$result);
$data = json_decode($payload[1],true);
$cash = $data['playerInfo']['cash'];
$gold = $data['map']['coins'];
$xp = $data['map']['xp'];
$level = $data['map']['level'];
$stone = $data['map']['stone'];
$wood = $data['map']['wood'];
$food = $data['map']['food'];
$str .= "Before > Slacker Games <br/>";
$str .= "*NAME*: ".$data['playerInfo']['name']."<br/>";
$str .= "*|CASH|* : ".number_format($data['playerInfo']['cash'],0,',','.')."<br/>";
$str .= "*|XP|* : ".number_format($data['map']['xp'],0,',','.')."<br/>";
$str .= "*|LEVEL|* : ".$data['map']['level']."<br/>";
$str .= "*|GOLD|* : ".number_format($data['map']['coins'],0,',','.')."<br/>";
$str .= "*|STONE|* : ".number_format($data['map']['stone'],0,',','.')."<br/>";
$str .= "*|WOOD|* : ".number_format($data['map']['wood'],0,',','.')."<br/>";
$str .= "*|FOOD|* : ".number_format($data['map']['food'],0,',','.')."<br/>";
$str .= "====================================<br/>";
$i=(integer)$_POST['level']+(integer)$_POST['xp']+(integer)$_POST['gold']+(integer)$_POST['wood']+(integer)$_POST['stone']+(integer)$_POST['food']+(integer)$_POST['cash'];
if($i==0) die("$str</body></html>");
if((integer)$_POST['level']>0){
$level=(integer)$_POST['level'];
if($level>100) $level=100;
$xp=0;
}else{
$xp+=(integer)$_POST['xp'];
if($xp<0) $xp=0;
$level = getLevelByXp($xp);
}
$cash+=(integer)$_POST['cash'];
if($cash<0) $cash=0;
$gold+=(integer)$_POST['gold'];
if($gold<0) $gold=0;
$stone+=(integer)$_POST['stone'];
if($stone<0) $stone=0;
$wood+=(integer)$_POST['wood'];
if($wood<0) $wood=0;
$food+=(integer)$_POST['food'];
if($food<0) $food=0;
$cmd[]=array('cmd'=>'set_variables','args'=>array($gold,$cash,$xp,$level,$stone,$wood,$food,0));
$datastr=json_encode(array('publishActions'=>"1",'commands'=>$cmd,'first_number'=>1,'tries'=>1,'ts'=>time(),'accessToken'=>""));
$hash = hash_hmac('sha256',$datastr,'3m0d3pwiupoetn7ysa02');
$postdata=http_build_query(array('data'=>$hash.';'.$datastr,'client_id'=>$cid));
$opts = array('http' =>
array(
'method' => 'POST',
'header' => "Content-type: application/x-www-form-urlencoded\r\n"."Content-Length: ".strlen($postdata)."\r\n",
'content' => $postdata
)
);
if(!sendPost($result,"ERROR 2001","http://dynamic01.socialpointgames.com/appsfb/socialempires/srvempires/command.php?USERID=$_POST[fbid]&spdebug=1",$opts)) die($result);
$postdata=http_build_query(array('neighbors'=>"",'client_id'=>$cid));
$opts = array('http' =>
array(
'method' => 'POST',
'header' => "Content-type: application/x-www-form-urlencoded\r\n"."Content-Length: ".strlen($postdata)."\r\n",
'content' => $postdata
)
);
if(!sendPost($result,"ERROR 2002","http://dynamic01.socialpointgames.com/appsfb/socialempires/srvempires/get_player_info.php?USERID=$_POST[fbid]&spdebug=1",$opts)) die($result);
$payload = explode(';',$result);
$data = json_decode($payload[1],true);
$str .= "After > Slacker Games <br/>";
$str .= "~NAME~: ".$data['playerInfo']['name']."<br/>";
$str .= "~|CASH|~ : ".number_format($data['playerInfo']['cash'],0,',','.')."<br/>";
$str .= "~|XP|~ : ".number_format($data['map']['xp'],0,',','.')."<br/>";
$str .= "~|LEVEL|~ : ".$data['map']['level']."<br/>";
$str .= "~|GOLD|~ : ".number_format($data['map']['coins'],0,',','.')."<br/>";
$str .= "~|STONE|~ : ".number_format($data['map']['stone'],0,',','.')."<br/>";
$str .= "~|WOOD|~ : ".number_format($data['map']['wood'],0,',','.')."<br/>";
$str .= "~|FOOD|~ : ".number_format($data['map']['food'],0,',','.')."<br/>";
$str .= "====================================<br/>";
die("$str</center></body></html>");
}else die("$str</body></html>");
function sendPost(&$result,$err="ERROR",$url,$args=array(),$timeout=30){
if(isset($args['http'])) $args['http']['timeout']=$timeout;
else if(isset($args['https'])) $args['https']['timeout']=$timeout;
else{
if(substr($url,0,5)=="https") $args['https']['timeout']=$timeout;
else $args['http']['timeout']=$timeout;
}
$result=file_get_contents($url,false,stream_context_create($args));
if(!$result){
$result=$err;
return false;
}
return true;
}
?>
