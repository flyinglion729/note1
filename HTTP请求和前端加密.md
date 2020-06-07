## ǰ�˵ļ��ܷ���
* ��������˵��ǰ�����κμ��ܴ����ǲ���ȫ�ģ���Ϊ�κ���Ϊ����Ϣ����Ĺ����ж��п��ܱ����ִ������ٳ�
* ���Ҿ�������ˣ��ڱ��ٳ���֮�����ܹ����ܵģ������ٴη�������
#### �����㷨
* �����㷨�͹�ϣ�㷨��һ���������㷨�ǿ���ģ����������֮�����ɵ����ĳ��Ⱥ����ı���ĳ����йأ�
* �������δ����Ҫ�������������ܽ��ܳ�������ʹ�ü����㷨
* �����㷨Ҳ�����֣��ԳƼ��ܺͷǶԳƼ���
###### �ԳƼ���
* �ԳƼ�����һ����򵥣����Ч�ļ��ܷ�ʽ����Ϊ���ܺͽ��ܶ���ʹ��ͬһ���ܳ�
* �ԳƼ���ͨ��ʹ�ý�С���ܳף�һ��С��256bit���������ܳ׳���ֻ��1bit���ڿ�ֻҪ����ʹ��0����1����������
* �����������ܳ׳���̫������1mb���ǺڿͿ�����Զ�޷����ܣ�������Ҳ��������Ľ���ʱ�䣬����Ϊ��Ȩ����������
* һ����ñȽ�С���ǲ����С���ܳ�
* ������һ��2000��������׼�ڼ����о��ᷢ���ĶԳƼ����㷨AES
```
//���ܹ���
private string myData = "hello"; 
private string myPassword = "OpenSesame"; 
private byte[] cipherText; 
private byte[] salt = { 
  0x0, 0x1, 0x2, 0x3, 0x4, 0x5, 0x6, 0x5, 0x4, 0x3, 0x2, 0x1, 0x0 
}; 
private void mnuSymmetricEncryption_Click(object sender, RoutedEventArgs e){ 
  var key = new Rfc2898DeriveBytes(myPassword, salt); 
  // Encrypt the data.
  var algorithm = new RijndaelManaged();
  algorithm.Key = key.GetBytes(16);
  algorithm.IV = key.GetBytes(16); 
  var sourceBytes = new System.Text.UnicodeEncoding().GetBytes(myData); 
  using (var sourceStream = new MemoryStream(sourceBytes)) 
  using (var destinationStream = new MemoryStream()) 
  using (var crypto = new CryptoStream(sourceStream, algorithm.CreateEncryptor(), CryptoStreamMode.Read)){
    moveBytes(crypto, destinationStream);
    cipherText = destinationStream.ToArray();
  }
  MessageBox.Show(String.Format(
    "Data:{0}{1}Encrypted and Encoded:{2}",myData,Environment.NewLine,Convert.ToBase64String(cipherText)
  ));
} 

private void moveBytes(Stream source, Stream dest){ 
  byte[] bytes = new byte[2048]; 
  var count = source.Read(bytes, 0, bytes.Length); 
  while (0 != count){
    dest.Write(bytes, 0, count);
    count = source.Read(bytes, 0, bytes.Length);
  }
}
```
```
//���ܹ���
private void mnuSymmetricDecryption_Click(object sender, RoutedEventArgs e){ 
  if (cipherText == null){
    MessageBox.Show("Encrypt Data First!"); 
    return;
  } 
  var key = new Rfc2898DeriveBytes(myPassword, salt); 
  // Try to decrypt, thus showing it can be round-tripped.
  var algorithm = new RijndaelManaged();
  algorithm.Key = key.GetBytes(16);
  algorithm.IV = key.GetBytes(16); 
  using (var sourceStream = new MemoryStream(cipherText)) 
  using (var destinationStream = new MemoryStream()) 
  using (var crypto = new CryptoStream(sourceStream, algorithm.CreateDecryptor(),CryptoStreamMode.Read)){
    moveBytes(crypto, destinationStream); 
    var decryptedBytes = destinationStream.ToArray(); 
    var decryptedMessage = new UnicodeEncoding().GetString(decryptedBytes);
    MessageBox.Show(decryptedMessage);
  }
}
```
* �����ĶԳƼ����㷨��DES��3DES��Blowfish��IDEA��RC4��RC5��RC6��AES
* ע�� ע�� ע�� ����ǰ�˵�͸���ԣ����ڵ�¼�����������Ϣ���Ͳ�Ҫʹ�����ֶԳƵķ�ʽ���м��ܣ���Ϊһ���кڿ����ص��ܳ׾���
* ֱ�ӽ��ܡ����жԳ��ܳ׵�һ��ȱ������ܳ׵Ĺ���ͷ��䣬���仰˵��������ô�������е��ܳ׽����Ǹ���Ҫ���ܵ��ˣ���;�Ǻ����ױ�
* �ڿͽ������صģ���ʵ������ͨ�����������ǽ��ܳ׽��зǶԳƼ��� emmm
###### �ǶԳƼ���
* �ǶԳƼ���Ϊ�����ṩ��һ�ַǳ���ȫ�ļ��ܷ������ǶԳƼ�����һ���ܳ���ɣ���Ϊ˽Կ�͹�Կ��˽Կ��һ����ȫ���ܣ�����й¶����
* ����Կ����Է����κ����������ˣ��ǶԳƼ����У���Կ�������ܣ���˽Կ����������
* ���磬�������㷢��һ����Կ�����ù�Կ���м��ܣ�Ȼ��������У�ֻ������ӵ�е�˽Կ�����������ܣ��������õ������ԿҲû�취
* ��Ϊ�ڴ���Ĺ����У����в���Ҫ����˽Կ�����԰�ȫ�Դ��������
* Ŀǰ��õķǶԳƼ��ܾ���RSA�㷨����������
```
//�����㷨
private byte[] rsaCipherText; private void mnuAsymmetricEncryption_Click(object sender, RoutedEventArgs e){ 
  var rsa = 1; 
  // Encrypt the data.
  var cspParms = new CspParameters(rsa);
  cspParms.Flags = CspProviderFlags.UseMachineKeyStore;
  cspParms.KeyContainerName = "My Keys"; 
  var algorithm = new RSACryptoServiceProvider(cspParms); 
  var sourceBytes = new UnicodeEncoding().GetBytes(myData);
  rsaCipherText = algorithm.Encrypt(sourceBytes, true);
  MessageBox.Show(String.Format(
    "Data: {0}{1}Encrypted and Encoded: {2}",myData,Environment.NewLine,Convert.ToBase64String(rsaCipherText)
  ));
```
```
//���ܹ���
private void mnuAsymmetricDecryption_Click(object sender, RoutedEventArgs e){ 
  if(rsaCipherText==null){
    MessageBox.Show("Encrypt First!"); 
    return;
  } 
  var rsa = 1; 
  // decrypt the data.
  var cspParms = new CspParameters(rsa);
  cspParms.Flags = CspProviderFlags.UseMachineKeyStore;
  cspParms.KeyContainerName = "My Keys"; 
  var algorithm = new RSACryptoServiceProvider(cspParms); 
  var unencrypted = algorithm.Decrypt(rsaCipherText, true);
  MessageBox.Show(new UnicodeEncoding().GetString(unencrypted));
}
```
* �����ķǶԳƼ����㷨�У�RSA��ECC���ƶ��豸�ã���Diffie-Hellman��El Gamal��DSA������ǩ���ã�
###### �Գ��ܳ׺ͷǶԳ��ܳ׳��÷�ʽ
* ��Ȼ�ǶԳ��ܳ׷ǳ��ķ��㣬�������ڽ��ܷǳ��Ļ���������һ�㻹��ʹ�öԳ��ܳ׽��д�����Ϣ������ʹ�÷ǶԳ��ܳ׸��Գ��ܳ׽��м���
* ����ȷ����ȫ��ͬʱҲ��ȷ�������ٶ�
```
����������һ�������ĳ���
1.Alice��Ҫȥ������һ�����ף��������������������һ���Գ��ܳף����ڽ���Ϣ���ݴ洢��ȥ��
2.Ȼ��Alice���������������Ҫһ���ǶԳƵĹ�Կ
3.���н���Կ����Alice
4.Alice��������������Կ�Ѹոմ洢����Ϣ�ĶԳ��ܳ׼���
5.Alice�ٰ�������ܺ�ĶԳ��ܳ׷�������
6.����ʹ��˽Կ��Alice�ļ���֮��ĶԳ��ܳ׽��ܻ�ȡ����
```
#### ��ϣ(hash)�����㷨
###### ��ϣ�㷨�ļ��
* ��ϣ�㷨�ǽ�Ŀ���ı�ת���ɾ�����ͬ���ȵ��ַ����������������۶೤���ı�������ͨ���㷨h����һ����ͬ���ȵ��ַ���
* ������һ�����һ��ӳ���ϵ����û�а취��������ģ����Թ�ϣ�㷨�ǲ�����ģ����ҵ�����ı��ʱ�򣬹�ϣ�㷨�ó��Ľ��Ҳ�ǲ�һ����
* ���Ի��ڹ�ϣ�㷨�����ԣ�ͨ������������Ҫת�������ĵ��ı�����
* �Ƚϳ��õĹ�ϣ�㷨��MD5 �� SHA1
```
�����ǱȽ���Ϥ��Ӧ�ó������ǣ������ǵ�½һ���Ѿ�ע�������վ��ʱ��ѡ����������
���ʱ����վ�ͻ����㷢��һ��������������һ�����伤�����ӣ������ǽ�֮ǰ���������뷢����
�������Ϊ��ϣ�㷨�ǲ������
```
* ע�� ע�� ע��  ��Ҫע����ǣ����Ҫ���������ʹ�ù�ϣ�㷨��ҲҪ�ڷ�����Ͻ��й�ϣ���ܣ����ܶ�Ӧ��
* ��Ϊ����һ�����������˾Ͳ���Ҫ������ת�������Ľ��жԱ������ˣ���һ������һ�������㷨���ܳ�й¶�������û����Ͽⶼ�൱�����Ĵ洢��
* ���ǰ�˴�������ֵ�����ģ���ôҲҪ�����ϣ��֮��洢���´ε�¼��ʱ���ϣ���жԱȼ���
###### ��ϣ�㷨�Ĺ�����ʽ
* ��ϣ�㷨�Ĺ�����ʽһ�������֣�Ѱ����ײ������ٷ�
* ����Ϊ�����ݵĽ�һ����ȫ�������ڹ�ϣ�㷨�Ļ����Ͻ�һ�����ܣ����н�һ�����ܵķ�ʽ���ǣ����Ρ�����ϣ���ܳ׹�ϣ��XOR��
###### ����
* ���μ����ǽ�ͬһ�������һ��nλ�����������ĵļ��ܷ�ʽ
* Ϊ�˸��õ���⣬����һƪ���½���˵��[](https://blog.csdn.net/jblock/article/details/78446604)
```
�������ǣ�
���û�����ע���ʱ����ϵͳ����һ�������Saltֵ��Ȼ��������⴮���ֵ������������Hashֵ
Ȼ��Hashֵ��Saltֵ�ֱ�洢�����ݿ���
���´ε�¼��ʱ�򣬻�ȡ���û��ĵ�¼��Ϣ���ٽ����ݿ���Ψһ��Saltֵ�����������һ��Hashֵ
�ٽ�����Hashֵ�����ݿ��е�Hashֵ���жԱȼ���
```
* ע�� ע�� ע�� �������ּ��εļ��ܷ�ʽ��Ҫע������
```
1.����ֵ��
�����ֵ̫�̣���ô�Ϳ���ʹ�ö�γ����������ƽ⣬����ֻ��3��ASCII�ַ�����ôֻ��95*95*95�ֿ��ܣ��ͺ����ױ�����
2.��ֵ����
����Ŀ�����Ĺ����У���ʱ����������ֵд��������ֻ�е�һ�����������Saltֵ���涼�ظ�ʹ�����ֵ�����
�����ļ��η�ʽ����Ч��
�ٸ����ӣ���������û���������һ���ģ���ô���Ǿͻ�����ͬ��Hashֵ����ô���ǾͿ����÷�����鷨���з�������
```
* ������ȷ�ļ��η�ʽӦ����ѭ���¹���
```
1.��ֵӦ��ʹ�ü��ܵİ�ȫα���������������������C���Ե�RAND����
2.��ֵ�����ı��У�һ��ʹ�ñ�׼�ļ��ܷ�ʽ
3.��ֵҪ�㹻�������������ã���ֵ����Ҫ����ϣֵ�������һ�������������ظ�
4.��ֵ����ɷ�����ṩ��ǰ��ȡֵʹ��
```
###### ʹ��MD5��ϣ�㷨��ʵ��
* ��һƪ�ܺõ�����˵�����:[](https://www.f2td.com/2018/11/13/encrypt-the-user-password-with-md5/)
* ����������MD5���ܷ�ʽnpm install md5 -S
* Ȼ��
#### ����base64����
* ���ϸ���������˵��base64�����Ǽ��ܣ�ֻ��˵�Ǳ���
* ��Js��Ҳ�з�������ͽ���base64
```
//1.����
var result = Base.encode('shotCat��˧!');  //--> "c2hvdENhdOWlveW4hSE="

//2.����
var result2 = Base.decode(result); //--> 'shotCat��˧!' û��,�Ҿ�����ô��Ҫ��!!!	
```
## HTTP��HTTPS
* ������һƪ�ܲ�������������������������������������
* [](https://juejin.im/post/5a069b6d51882509e5432656)