layout: ew
title: Java加密算法
date: 2016-06-02 18:28:59
tags:
	- Java
	- 加密算法
categories:
	- JAVA
---

大体上分为双向加密和单向加密，而双向加密又分为对称加密和非对称加密。双向加密大体意思就是明文加密后形成密文，可以通过算法还原成明文。而单向加密只是对信息进行了摘要计算，不能通过算法生成明文。
<!--more-->
# 双向加密

## 对称加密

采用单钥密码系统的加密方法，同一个密钥可以同时用作信息的加密和解密，这种加密方式称为单向加密，也称为单密钥加密，需要对加密和解密使用相同密钥的加密算法。由于其速度，对称加密通常在消息发送方需要加密大量数据时使用。对称性加密也称为密钥加密。

常用的对称加密有：DES、IDEA、RC2、RC4、SKIPJACK、RC5、AES算法等。

对称加密一般类中定义成员：
```java
	//KeyGenerator提供对称密钥生成器的功能，支持各种算法
	private KeyGenerator keygen;
    //Secret负责保存对称密钥
    private SecretKey deskey;
    //Cipher负责完成加密或解密工作
    private Cipher c;
    //该字节数组负责保存加密结果
    private byte[] cipherByte;
```
在构造函数中初始化
```java
	Security.addProvider(new com.sun.crypto.provider.SunJCE());
    //实例化支持DES算法的密钥生成器（算法名称需要按规定，否则抛出异常）
    keygen = KeygenGenerator.getInstance("DES");
    //生成密钥
    deskey = keygen.generateKey();
    //生成Cipher对象，指定其支持的DES算法
    c = Cipher.getInstance("DES");
```

1. DES算法为密码体制中的对称密码体制，又被称为美国数据加密标准。明文按64位进行分组，密钥长64位，密钥事实上是56位参与DES运算分组后的明文组和56位的密钥按位替代或及哦啊换的方法形成密文组的加密方法。

```java
import java.security.InvalidKeyException;
import java.security.Security;
import javax.crypto.BadPaddingException;
import javax.crypto.Cipher;
import javax.crypto.IllegalBlockSizeException;
import javax.crypto.KeyGenerator;
import javax.crypto.SecretKey;

public class EncrypDES {
	
	//KeyGenerator提供对称密钥生成器的功能，支持各种算法
	private KeyGenerator keygen;
	//负责保存生产的密钥
	private SecretKey secretKey;
	//负责完成加密或者解密工作
	private Cipher cipher;
	//负责保存加密后的结果
	private byte[] cipherByte;
	
	
	public KeyGenerator getKeygen() {
		return keygen;
	}
	public void setKeygen(KeyGenerator keygen) {
		this.keygen = keygen;
	}
	public SecretKey getSecretKey() {
		return secretKey;
	}
	public void setSecretKey(SecretKey secretKey) {
		this.secretKey = secretKey;
	}
	public Cipher getCipher() {
		return cipher;
	}
	public void setCipher(Cipher cipher) {
		this.cipher = cipher;
	}
	public byte[] getCipherByte() {
		return cipherByte;
	}
	public void setCipherByte(byte[] cipherByte) {
		this.cipherByte = cipherByte;
	}
	
	public EncrypDES() throws Exception{
		Security.addProvider(new com.sun.crypto.provider.SunJCE());
		//实例化支持DES算法的密钥生成器
		keygen = KeyGenerator.getInstance("DES");
		//生成密钥
		secretKey = keygen.generateKey();
		//指定Cipher支持的DWS算法
		cipher = Cipher.getInstance("DES");
	}
	
	/**
	 * 对字符串进行加密
	 * @throws InvalidKeyException 
	 * @throws BadPaddingException 
	 * @throws IllegalBlockSizeException 
	 * 
	 */
	public byte[] Encryptor(String str) throws InvalidKeyException, IllegalBlockSizeException, BadPaddingException{
		//根据密钥，对Cipher对象进行初始化，ENCRYPT_MODE表示加密模式
		cipher.init(Cipher.ENCRYPT_MODE, secretKey);
		byte[] src = str.getBytes();
		//加密，将结果存入cipherByte
		cipherByte = cipher.doFinal(src);
		return cipherByte;
	}
	
	/**
	 * 对字符串解密
	 * @throws InvalidKeyException 
	 * @throws BadPaddingException 
	 * @throws IllegalBlockSizeException 
	 * 
	 */
	public byte[] Decriptor(byte[] buff) throws InvalidKeyException, IllegalBlockSizeException, BadPaddingException{
		
		cipher.init(Cipher.DECRYPT_MODE, secretKey);
		cipherByte = cipher.doFinal(buff);
		return cipherByte;
	}
	
	public static void main(String[] args) throws Exception{
		
		EncrypDES del = new EncrypDES();
		String msg = "Tsubasa";
		byte[] encontent = del.Encryptor(msg);
		byte[] decontent = del.Decriptor(encontent);
		System.out.println("明文是："+msg);
		System.out.println("加密后："+new String(encontent));
		System.out.println("解密后："+new String(decontent));
	}
}
```

2. 3DES又称为Triple DES，是DES加密算法的一种模式。它使用3条56位的密钥对3DES数据进行三次加密，是DES一个更安全的变形。它以DES为基本模块，通过组合分组方法设计出分组加密算法。

```java
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.security.Security;
import javax.crypto.BadPaddingException;
import javax.crypto.Cipher;
import javax.crypto.IllegalBlockSizeException;
import javax.crypto.KeyGenerator;
import javax.crypto.NoSuchPaddingException;
import javax.crypto.SecretKey;

public class EncrypDES3 {
	
	private KeyGenerator keyGenerator;
	private SecretKey secretKey;
	private Cipher cipher;
	private byte[] cipherByte;
	
	@SuppressWarnings("restriction")
	public EncrypDES3() throws NoSuchAlgorithmException, NoSuchPaddingException{
		Security.addProvider(new com.sun.crypto.provider.SunJCE());
		keyGenerator = KeyGenerator.getInstance("DESede");
		secretKey = keyGenerator.generateKey();
		cipher = Cipher.getInstance("DESede");
	}
	
	public byte[] Encryptor(String str) throws InvalidKeyException, IllegalBlockSizeException, BadPaddingException{
		
		cipher.init(Cipher.ENCRYPT_MODE, secretKey);
		cipherByte = cipher.doFinal(str.getBytes());
		return cipherByte;
	}
	
	public byte[] Decryptor(byte[] msg) throws IllegalBlockSizeException, BadPaddingException, InvalidKeyException{
		cipher.init(Cipher.DECRYPT_MODE, secretKey);
		cipherByte = cipher.doFinal(msg);	
		return cipherByte;
	}
	
	public static void main(String[] args) throws InvalidKeyException, IllegalBlockSizeException, BadPaddingException, NoSuchAlgorithmException, NoSuchPaddingException{
		EncrypDES3 encrypDES3 = new EncrypDES3();
		String msg = "Tsubasa";
		byte[] secret = encrypDES3.Encryptor(msg);
		byte[] result = encrypDES3.Decryptor(secret);
		System.out.println("明文是："+msg);
		System.out.println("密文是："+new String(secret));
		System.out.println("解密后："+new String(result));
	}
}
```

## 非对称加密

1976年，美国学者为解决信息公开传送和密钥管理问题，提出一种新的密钥交换协议，允许在不安全的媒体上的通讯双方交换信息，安全的达成一致的密钥，这就是“公开密钥系统”。相对于“对称加密算法”，也叫“非对称加密算法”。非对称加密算法需要两个密钥：公开密钥和私有密钥。公钥和私钥是一对，如果用公钥对数据进行加密，只有对应的私钥才能解密；如果用私钥加密，只有用对应的公钥解密。因为公钥和私钥是两个不同的密钥，所以叫做非对称加密算法。

1. RSA公钥加密算法是目前最具影响力的公钥加密算法，它能够抵抗到目前为止已知的所有密码攻击，已经被ISO推荐为公钥数据加密标准。RSA算法基于一个十分简单的数论事实：将两个大素数相乘十分容易，但想要对其乘积进行因式分解却极其困难，因此可以将乘积公开作为加密密钥。

```java
import java.security.InvalidKeyException;
import java.security.KeyPair;
import java.security.KeyPairGenerator;
import java.security.NoSuchAlgorithmException;
import java.security.interfaces.RSAPrivateKey;
import java.security.interfaces.RSAPublicKey;
import javax.crypto.BadPaddingException;
import javax.crypto.Cipher;
import javax.crypto.IllegalBlockSizeException;
import javax.crypto.NoSuchPaddingException;

public class EncrypRSA {
	
	public byte[] encrypt(RSAPublicKey publicKey,byte[] srcBytes) throws NoSuchAlgorithmException, NoSuchPaddingException, InvalidKeyException, IllegalBlockSizeException, BadPaddingException{
		
		if(publicKey != null){
			Cipher cipher = Cipher.getInstance("RSA");
			cipher.init(Cipher.ENCRYPT_MODE, publicKey);
			byte[] result = cipher.doFinal(srcBytes);
			return result;
		}
		return null;
	}
	
	public byte[] decrypt(RSAPrivateKey privateKey,byte[] srcBytes) throws Exception{
		
		if(privateKey != null){
			Cipher cipher = Cipher.getInstance("RSA");
			cipher.init(Cipher.DECRYPT_MODE, privateKey);
			byte[] reslut = cipher.doFinal(srcBytes);
			return reslut;
		}
		return null;
	}
	
	public static void main(String[] args) throws Exception{
		
		EncrypRSA rsa = new EncrypRSA();
		String msg = "Tsubasa";
		//用于生成公钥和私钥对，基于RSA算法生成对象
		KeyPairGenerator keyPairGen = KeyPairGenerator.getInstance("RSA");
		//初始化密钥生成器对，密钥大小为1024位
		keyPairGen.initialize(1024);
		//生成一个密钥对，保存在keypair中
		KeyPair keyPair = keyPairGen.generateKeyPair();
		RSAPrivateKey rsaPrivateKey =(RSAPrivateKey) keyPair.getPrivate();
		RSAPublicKey rsaPublicKey =(RSAPublicKey) keyPair.getPublic();
		
		//用公钥加密
		byte[] srcBytes = msg.getBytes();
		byte[] resultBytes = rsa.encrypt(rsaPublicKey, srcBytes);
		//用私钥解密
		byte[] deBytes = rsa.decrypt(rsaPrivateKey, resultBytes);
		System.out.println("明文是："+msg);
		System.out.println("密文是："+new String(resultBytes));
		System.out.println("解密后："+new String(deBytes));
		
	}

}

```

# 单向加密

1. MD5即Message-Digest Algorithm 5（信息摘要算法5），用于确保信息传输完整一致，，是计算机广泛使用的杂凑算法之一。MD5作用是让大容量信息在数字签名软件签署私人密钥前被“压缩”成一种保密的格式。

```java
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;

public class EncrypMD5 {
	
	public byte[] eccrpt(String info) throws NoSuchAlgorithmException{
		MessageDigest md5 = MessageDigest.getInstance("MD5");
		byte[] srcBytes = info.getBytes();
		md5.update(srcBytes);
		byte[] result = md5.digest();
		return result;
	}
	
	public static void main(String[] args) throws NoSuchAlgorithmException{
		String msg = "Tsubasa";
		EncrypMD5 encrypMD5 = new EncrypMD5();
		byte[] resultBytes = encrypMD5.eccrpt(msg);
		System.out.println("明文是："+msg);
		System.out.println("密文是："+new String(resultBytes));
	}

}

```