# AWS의 지식을 Azure에서도 활용
이 repo는 2017년 4월 28일 예정된 Microsoft Tech Summit의 세션에 대한 코드와 hands on 자료를 제공  

- Microsoft Tech Summit Seoul : https://www.microsoft.com/ko-kr/techsummit/seoul.aspx?wt.mc_id=AID560140_owned_AzureEvents_Copy
- Short URL : http://aka.ms/techsummit-port-aws
- Github URL : https://github.com/CloudBreadPaPa/port-aws-to-azure
- Slideshare : <준비중>

## Network 비교
### Virtual Network 생성과 비교  
[Azure 가상 네트워크](https://azure.microsoft.com/ko-kr/services/virtual-network/)   

### Azure 리소스 그룹으로 VM 생성
[Azure 퀵 스타트 템플릿](https://azure.microsoft.com/ko-kr/resources/templates/?term=sql)  

2개의 웹서버를 위한 VM과 1개의 SQL VM을 생성하는 템플릿  
[Create 2 VMs in LB and a SQL Server VM with NSG](https://azure.microsoft.com/ko-kr/resources/templates/301-2fe-lb80-rdp-1be-nsg-rdp/)  

# Compute 비교
## AWS Elastic Beanstalk - Azure Web App

- Web App을 생성  
https://azure.microsoft.com/en-us/documentation/articles/app-service-web-get-started/  

### Source control
- 생성한 Web App에서 FTP, Git, Github 등으로 배포  
FTP : https://azure.microsoft.com/en-us/documentation/articles/web-sites-deploy/  
Git : https://azure.microsoft.com/en-us/documentation/articles/app-service-deploy-local-git/  
```
git init  
git add .  
git commit -m "initial commit"  
git remote add azure [URL for remote repository]  
git push azure master  
//코드 수정 작업 후  
git add .  
git commit -m "second commit"  
git push azure master  
//배포 확인  
```
Github : https://azure.microsoft.com/en-us/documentation/articles/app-service-web-arm-from-github-provision/  
```
repo를 github에 생성  
Azure portal에서 연속 배포 설정  
설정 후 github의 repo를 Azure Portal에서 지정  
```

### 다양한 언어 지원
- Web App과 C# - ASP.NET
https://azure.microsoft.com/en-us/documentation/articles/web-sites-dotnet-get-started/  
```
VS에서 ASP.NET 프로젝트 생성  
빌드, 디버그, 실행  
생성된 Project를 VS에서 게시 수행  
```

- Web App과 PHP  
https://azure.microsoft.com/en-us/documentation/articles/app-service-web-php-get-started/  
```
index.php 생성  
phpinfo() 코드 추가  
WebMatrix 또는 git을 이용해 publish 수행  
Web App 설정에서 php version 및 php 관련 cofnig 수행  
```


- Web App과 node.js  
https://azure.microsoft.com/en-us/documentation/articles/app-service-web-nodejs-get-started/  
```
app.js 생성  
console.log 코드 추가  
git 등을 이용해 publish 수행  
```


### 크기조절 및 테스트
Web App 크기조절  
https://azure.microsoft.com/en-us/documentation/articles/web-sites-scale/  
```
// Web App의 App Service Plan과 크기조절
```
참고정보 : Pattern & Practice - Transient Fault Handling  
https://msdn.microsoft.com/en-us/library/dn440719(v=pandp.60).aspx 

## AWS Lambda - Azure Functions
### Azure Functions 를 이용한 개발
- C#, F#, Node.js, Python or PHP 언어 지원  
- Pay Per User 과금 방식
- Dependency 구성 가능
- Active Directory, Facebook, Google, Twitter, and Microsoft Account 인증 통합
- 오픈소스 기반 런타임 - Github 제공


# 저장소 비교
## AWS S3 - Azure Blob Storage
### Azure blob 저장소
Azure Storage 개발에 유용한 Storage Explorer 도구 다운로드 
http://storageexplorer.com/  

Storage Exploerer 설치 후 Azure Portal에서 저장소 계정(Storage Account)의 엑세스 키 - 연결문자열을 넣으면 해당 저장소 계정에 Storage Explorer로 바로 접근 가능합니다.  

#### C# 코드  
참고링크 : https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-blobs/  
```
public static void CreateContainer()
{
    // 저장소 연결 문자열 처리
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // client 개체 생성
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // 컨테이너 개체 참조
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // 컨테이너가 없으면 생성
    container.CreateIfNotExists();

    return;
}
```

#### node.js
```
// 아래 링크에서 정보 확인 https://azure.microsoft.com/en-us/documentation/articles/storage-nodejs-how-to-use-blob-storage/

// 설치
// npm install azure-storage

// 패키지 import 수행
var azure = require('azure-storage');

// connection string 작업 
// environment variable을 구성 | Azure 위에서 구성도 가능
var tableService = azure.createTableService('<저장소이름>', '<저장소키>');

// 테이블 생성
tableService.createTableIfNotExists('nodetable', function(error, result, response) {
  if (!error) {
    // 수행완료
	console.log('ok');
	console.log(response);
  } else {
	  console.log(error);
  }
});
```
#### PHP
```
<?php
// composer를 이용해 PHP Azure client를 설치
// https://azure.microsoft.com/en-us/documentation/articles/storage-php-how-to-use-blobs/

require_once 'vendor\autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Blob\Models\CreateContainerOptions;
use WindowsAzure\Blob\Models\PublicAccessType;
use WindowsAzure\Common\ServiceException;

// https://azure.microsoft.com/en-us/documentation/articles/storage-php-how-to-use-blobs/

// Storage의 connection string 제공
$connectionString = "DefaultEndpointsProtocol=http;AccountName=<저장소이름>;AccountKey=<저장소키>";

// REST proxy 생성
$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

$createContainerOptions = new CreateContainerOptions();

//setPublicAccess 접근 정책 설정

// CONTAINER_AND_BLOBS:
// 전체 엑세스 권한
//
// BLOBS_ONLY:
// blob들에 대해서만 읽기 권한. 
$createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

// 컨테이너 메타데이터 설정
$createContainerOptions->addMetaData("VSTechUp", "Visual Studio");
$createContainerOptions->addMetaData("Azure", "Cloud");

try {
    // 컨테이너 생성
    $blobRestProxy->createContainer("phpcontainer", $createContainerOptions);
}
catch(ServiceException $e){
    // 에러 핸들링
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

?>

```

### Azure table 저장소
#### C# 코드  
참고링크 : https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-tables/
```
// 저장소 연결 문자열 처리
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// table 클라이언트 개체 생성
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// 테이블 참조 설정 
CloudTable table = tableClient.GetTableReference("people");

// 테이블이 존재하지 않으면 생성
table.CreateIfNotExists();
```
#### node.js
```
// 아래 링크에서 정보 확인 https://azure.microsoft.com/en-us/documentation/articles/storage-nodejs-how-to-use-blob-storage/

// 설치
// npm install azure-storage

// 패키지 import 수행
var azure = require('azure-storage');

// connection string 작업 
// environment variable을 구성 | Azure 위에서 구성도 가능
var tableService = azure.createTableService('<저장소계정>', '<저장소키>');

// 테이블 생성
tableService.createTableIfNotExists('nodetable', function(error, result, response) {
  if (!error) {
    // 수행완료
	console.log('ok');
	console.log(response);
  } else {
	  console.log(error);
  }
});
```

#### PHP
```
<?php
// https://azure.microsoft.com/en-us/documentation/articles/storage-php-how-to-use-table-storage/
require_once 'vendor\autoload.php';
//ini_set('display_errors', 1);
//error_reporting(~0);

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;

// Storage의 connection string 제공
$connectionString = "DefaultEndpointsProtocol=http;AccountName=<저장소계정>;AccountKey=<어카운트키>";

// Azure의 table storage를 위한 REST proxy 생성
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

/////////////////////////////////////////////////////////////////
// 01 테이블 생성
/////////////////////////////////////////////////////////////////
try {
    // 테이블 생성
    $tableRestProxy->createTable("phptable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
?>
```

### Azure queue 저장소
#### C# 참고링크
https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-queues/  
```
public static void CreateQueue()
{
    // 연결 문자열 처리
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 큐 클라이언트 개체 생성
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // 큐 참조 설정
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // 큐가 없으면 생성
    queue.CreateIfNotExists();
}
```

#### node.js 참고링크
https://azure.microsoft.com/en-us/documentation/articles/storage-nodejs-how-to-use-queues/  
```
todo  
```

#### PHP 참고링크
https://azure.microsoft.com/en-us/documentation/articles/storage-php-how-to-use-queues/  
```
todo  
```

### Azure files
참고링크 : https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-files/  
주의사항으로, outbound 445 가 열려 있어야 하는 제한이 있으며, ISP에 따라서 다름.  

# Database 비교
## AWS RDS - Azure SQL 데이터 베이스(SQL Database)  
### SQL Database  
소개 및 생성  
VS 또는 SSMS에서 연결  
SQL Database vs VM  

#### C# 코드
참고링크 : https://msdn.microsoft.com/library/mt718320.aspx  
```
using System;
using C = System.Data.SqlClient;

namespace azure_sql_cs
{
    class Program
    {
        static void Main(string[] args)
        {
            using (var connection = new C.SqlConnection(
                "Server=tcp:서버명.database.windows.net,1433;Initial Catalog=DB명;Persist Security Info=False;User ID=유저아이디;Password=비밀번호;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"
                    ))  // 연결 문자열을 복사하고 Azure SQL Database 생성시 지정한 user id와 pwd로 변경. 서버명과 DB명은 자동으로 연결 문자열에 지정
            {
                connection.Open();
                Console.WriteLine("Connected successfully.");

                Console.WriteLine("Press any key to finish...");
                Console.ReadKey(true);
            }
        }
    }
}
```
#### PHP
참고링크 : https://msdn.microsoft.com/library/mt720665.aspx  
```
<?php 
function OpenConnection()  
{  
	try  
	{  
		$serverName = "tcp:서버명.database.windows.net,1433";  
		$connectionOptions = array("Database"=>"DB명",  
			"Uid"=>"유저아이디", "PWD"=>"비밀번호");  
		$conn = sqlsrv_connect($serverName, $connectionOptions);  
		if($conn == false)  
			die(FormatErrors(sqlsrv_errors()));  
	}  
	catch(Exception $e)  
	{  
		echo("Error!");  
	}  
}  
?>
```

#### node
참고링크 :  
https://msdn.microsoft.com/library/mt715784.aspx   
https://msdn.microsoft.com/library/mt652094.aspx   

```
npm install tedious
```
```
var Connection = require('tedious').Connection;  
var config = {  
	userName: 'yourusername',  
	password: 'yourpassword',  
	server: 'yourserver.database.windows.net',  
	// If you are on Microsoft Azure, you need this:  
	options: {encrypt: true, database: 'AdventureWorks'}  
};  
var connection = new Connection(config);  
connection.on('connect', function(err) {  
// If no error, then good to proceed.  
	console.log("Connected");  
});  
```

- Elastic Pool  
- OSS에서 연결 : https://azure.microsoft.com/en-us/documentation/articles/sql-database-libraries/  


문서의 끝



