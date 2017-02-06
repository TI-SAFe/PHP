# PHP
Exemplo de integração SAFe com PHP.

```PHP
<?php
function RequestToken(){
	$endpoint = 'https://demonstracaosafe.bvsnet.com.br/token';
	
	$params = array(
		"username" => "usuario",
		"password" => "senha",
		"grant_type" => "password"
	);
	$curl = curl_init($endpoint);
	curl_setopt($curl, CURLOPT_HEADER, true);
	curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
	curl_setopt($curl, CURLOPT_POST, true);
	curl_setopt($curl, CURLOPT_HEADER, 'Content-Type: application/x-www-form-urlencoded');
	curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, 0);
	curl_setopt($curl, CURLOPT_SSL_VERIFYHOST, 2);
	
	$postData = "";
	foreach ($params as $k => $v) {
		$postData .= $k . '='.urlencode($v).'&';
	}
	curl_setopt($curl, CURLOPT_POSTFIELDS, $postData);
	
	$json_response = curl_exec($curl);
	curl_close($curl);
	return $json_response;
}
            
function PostPedido(){
	$jsonPedido = '{
		...
	}';
	$oauthToken = json_decode(RequestToken(), true);	
	$obj = json_decode($jsonPedido);
	$service_url = 'https://demonstracaosafe.bvsnet.com.br/api/Pedido/Enviar';
	$request = curl_init($service_url);
	curl_setopt($request, CURLOPT_HEADER, true);
	curl_setopt($request, CURLOPT_HEADER, 'Content-Type: application/json');
	curl_setopt($request, CURLOPT_HTTPHEADER, array("Accept: application/json"));
	curl_setopt($request, CURLOPT_HTTPHEADER, array("Authorization: Bearer ".$oauthToken["access_token"]));
	curl_setopt($request, CURLOPT_RETURNTRANSFER, true);
	curl_setopt($request, CURLOPT_POST, true);
	curl_setopt($request, CURLOPT_SSL_VERIFYPEER, 0);
	curl_setopt($request, CURLOPT_SSL_VERIFYHOST, 2);
	curl_setopt($request, CURLOPT_POSTFIELDS, http_build_query($obj));
	try {
		$result = curl_exec($request);
		$info = curl_getinfo($request);
	} catch (\Exception $e) {
		$result = $e->getMessage();
		echo $e;
	}
}

PostPedido();      
?>
```
