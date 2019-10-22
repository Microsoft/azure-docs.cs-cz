---
title: Azure Cosmos DB의 API for MongoDB를 사용하여 Angular 앱 만들기 - Mongoose를 사용하여 Cosmos DB에 연결
titleSuffix: Azure Cosmos DB
description: 이 자습서에는 Angular 및 Express를 사용하여 Cosmos DB에 저장된 데이터를 관리하는 Node.js 애플리케이션을 빌드하는 방법을 설명합니다. 여기서는 Mongoose를 사용하여 Azure Cosmos DB에 연결합니다.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
Customer intent: As a developer, I want to build a Node.js application, so that I can manage the data stored in Cosmos DB.
ms.openlocfilehash: 626015e2aac5eb09dfd271a139dbc5eb49a088fc
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "69616424"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>Azure Cosmos DB의 API for MongoDB를 사용하여 Angular 앱 만들기 - Mongoose를 사용하여 Cosmos DB에 연결

여러 파트로 구성된 이 자습서에서는 Express 및 Angular를 사용하여 Node.js 앱을 만들고, [Cosmos DB의 API for MongoDB를 사용하여 구성된 Cosmos 계정](mongodb-introduction.md)에 연결하는 방법을 보여줍니다. 이 문서는 자습서의 5부를 설명하며 [4부](tutorial-develop-mongodb-nodejs-part4.md)를 기반으로 합니다.

이 자습서의 이번 회차에서는 다음과 같은 일을 합니다.

> [!div class="checklist"]
> * Mongoose를 사용하여 Cosmos DB에 연결합니다.
> * Cosmos DB 연결 문자열을 가져옵니다.
> * Hero 모델을 만듭니다.
> * Hero 데이터를 가져오는 Hero 서비스를 만듭니다.
> * 로컬로 앱 실행

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>전제 조건

* 이 자습서를 시작하기 전에 [4부](tutorial-develop-mongodb-nodejs-part4.md)의 단계를 완료해야 합니다.

* 이 자습서에서는 Azure CLI를 로컬로 실행해야 합니다. Azure CLI 버전 2.0 이상이 설치되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

* 이 자습서에서는 애플리케이션을 빌드하는 방법을 단계별로 안내합니다. 완료된 프로젝트를 다운로드하려는 경우 GitHub의 [angular-cosmosdb repo](https://github.com/Azure-Samples/angular-cosmosdb)에서 완성된 애플리케이션을 가져올 수 있습니다.

## <a name="use-mongoose-to-connect"></a>Mongoose를 사용하여 연결

Mongoose는 MongoDB 및 Node.js에 사용되는 ODM(개체 데이터 모델링) 라이브러리입니다. Mongoose를 사용하여 Azure Cosmos DB 계정에 연결할 수 있습니다. 다음 단계에 따라 Mongoose를 설치하고 Azure Cosmos DB에 연결할 수 있습니다.

1. MongoDB와 통신하는 데 사용되는 API인 mongoose npm 모듈을 설치합니다.

    ```bash
    npm i mongoose --save
    ```

1. **server** 폴더에 **mongo.js**라는 파일을 만듭니다. 이 파일에 Azure Cosmos DB 계정의 연결 세부 정보를 추가할 것입니다.

1. 다음 코드를 **mongo.js** 파일에 복사합니다. 이 코드는 다음과 같은 기능을 제공합니다.

   * Mongoose가 필요합니다.
   * ES6/ES2015 이상 버전에 빌드된 기본 프라미스를 사용하도록 Mongo 프라미스를 재정의합니다.
   * 준비, 프로덕션, 개발 중 어떤 단계에 있는지에 따라 특정 작업을 설정할 수 있는 env 파일을 호출합니다. 이 파일은 다음 섹션에서 만들 것입니다.
   * env 파일에서 설정되는 MongoDB 연결 문자열을 포함합니다.
   * Mongoose를 호출하는 연결 함수를 만듭니다.

     ```javascript
     const mongoose = require('mongoose');
     /**
     * Set to Node.js native promises
     * Per https://mongoosejs.com/docs/promises.html
     */
     mongoose.Promise = global.Promise;

     const env = require('./env/environment');

     // eslint-disable-next-line max-len
     const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

     function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
     }

     module.exports = {
     connect,
     mongoose
     };
     ```
    
1. 탐색기 창에서 **server** 아래에 **environment**라는 폴더를 만듭니다. **environment** 폴더에 **environment.js**라는 파일을 만듭니다.

1. mongo.js 파일에서 `dbName`, `key` 및 `cosmosPort` 매개 변수의 값을 포함해야 합니다. 다음 코드를 **environment.js** 파일에 복사합니다.

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string"></a>연결 문자열 가져오기

애플리케이션을 Azure Cosmos DB에 연결하려면 애플리케이션의 구성 설정을 업데이트해야 합니다. 다음 단계에 따라 설정을 업데이트합니다. 

1. Azure Portal에서 Azure Cosmos DB 계정의 포트 번호, Azure Cosmos DB 계정 이름 및 기본 키 값을 가져옵니다.

1. **environment.js** 파일에서 `port` 값을 10255로 변경합니다. 

    ```javascript
    const port = 10255;
    ```

1. **environment.js** 파일에서 `accountName` 값을 이 자습서의 [4부](tutorial-develop-mongodb-nodejs-part4.md)에서 만든 Azure Cosmos DB 계정 이름으로 변경합니다. 

1. 터미널 창에서 다음 CLI 명령을 사용하여 Azure Cosmos DB 계정에 대한 기본 키를 검색합니다. 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb-name>은 이 자습서의 [4부](tutorial-develop-mongodb-nodejs-part4.md)에서 만든 Azure Cosmos DB 계정의 이름입니다.

1. 기본 키를 **environment.js** 파일에 `key` 값으로 복사합니다.

이제 애플리케이션을 Azure Cosmos DB에 연결하는 데 필요한 모든 정보가 준비되었습니다. 

## <a name="create-a-hero-model"></a>Hero 모델 만들기

다음으로, 모델 파일을 정의하여 Azure Cosmos DB에 저장할 데이터의 스키마를 정의해야 합니다. 다음 단계에 따라 데이터의 스키마를 정의하는 _Hero 모델_을 만듭니다.

1. 탐색기 창의 **server** 폴더 아래에 **hero.model.js**라는 파일을 만듭니다.

1. 다음 코드를 **hero.model.js** 파일에 복사합니다. 이 코드는 다음과 같은 기능을 제공합니다.

   * Mongoose가 필요합니다.
   * ID, 이름 및 설명을 포함한 새 스키마를 만듭니다.
   * 스키마를 사용하여 모델을 만듭니다.
   * 모델을 내보냅니다. 
   * **Heroes** 컬렉션의 이름을 지정합니다(Mongoose 복수형 명명 규칙에 따른 컬렉션의 기본 이름인 **Heros** 대신 다른 이름).

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>Hero 서비스 만들기

hero 모델을 만든 후에는 데이터를 읽고 나열, 만들기, 삭제 및 업데이트 작업을 수행하는 서비스를 정의해야 합니다. 다음 단계에 따라 Azure Cosmos DB에서 데이터를 쿼리하는 _Hero 서비스_를 만듭니다.

1. 탐색기 창의 **server** 폴더 아래에 **hero.service.js**라는 파일을 만듭니다.

1. 다음 코드를 **hero.service.js** 파일에 복사합니다. 이 코드는 다음과 같은 기능을 제공합니다.

   * 만든 모델을 가져옵니다.
   * 데이터베이스에 연결합니다.
   * `hero.find` 메서드를 사용하여 모든 Heroes를 반환하는 쿼리를 정의하는 `docquery` 변수를 만듭니다.
   * 모든 Heroes 목록을 가져오려면 프라미스에서 `docquery.exec` 함수를 사용하여 쿼리를 실행합니다. 여기서 응답 상태는 200입니다. 
   * 상태가 500인 경우 오류 메시지를 다시 보냅니다.
   * 모듈을 사용 중이므로 Heroes를 가져옵니다. 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="configure-routes"></a>경로 구성

다음으로, 가져오기, 만들기, 읽기 및 삭제 요청에 대한 URL을 처리하는 경로를 설정해야 합니다. 라우팅 메서드는 콜백 함수(_처리기 함수_라고도 함)를 지정합니다. 애플리케이션이 지정된 엔드포인트 및 HTTP 메서드에 대한 요청을 수신하면 이러한 함수가 호출됩니다. 다음 단계에 따라 Hero 서비스를 추가하고 경로를 정의합니다.

1. Visual Studio Code의 **routes.js** 파일에서 샘플 Hero 데이터를 보내는 `res.send` 함수를 주석으로 처리합니다. `heroService.getHeroes` 함수를 대신 호출하는 줄을 추가합니다.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. **routes.js** 파일에서 Hero 서비스를 `require`합니다.

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. **hero.service.js** 파일에서 다음과 같이 `req` 및 `res` 매개 변수를 사용하도록 `getHeroes` 함수를 업데이트합니다.

    ```javascript
    function getHeroes(req, res) {
    ```

여기서 잠시 시간을 내어 이전 코드를 검토하고 살펴보겠습니다. 노드 서버를 설정하는 index.js 파일부터 보겠습니다. 이 파일은 경로를 설정하고 정의합니다. 다음으로, routes.js 파일은 Hero 서비스와 통신하고 **getHeroes** 같은 함수를 가져와서 요청 및 응답을 전달하도록 지시합니다. hero.service.js 파일은 모델을 가져와서 Mongo에 연결합니다. 그런 다음, 사용자의 호출이 있으면 **getHeroes**를 실행하고 응답 200을 반환합니다. 

## <a name="run-the-app"></a>앱 실행

다음 단계에 따라 앱을 실행합니다.

1. Visual Studio Code에서 모든 변경 내용을 저장합니다. 왼쪽에서 **디버그** 단추 ![Visual Studio Code의 디버그 아이콘](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png)를 선택한 다음, **디버깅 시작** 단추 ![Visual Studio Code의 디버그 아이콘](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png)를 선택합니다.

1. 이제 브라우저로 전환합니다. **개발자 도구** 및 **네트워크 탭**을 엽니다. @No__t_2로 이동 하면 응용 프로그램이 표시 됩니다.

    ![Azure Portal의 새 Azure Cosmos DB 계정](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

아직 앱에 저장된 Heroes가 없습니다. 이 자습서의 다음 파트에서는 배치, 푸시 및 삭제 기능을 추가하겠습니다. 그런 다음, Mongoose를 Azure Cosmos 데이터베이스에 연결하여 UI에서 Heroes를 추가, 업데이트 및 삭제할 수 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리

리소스가 더 이상 필요하지 않은 경우 리소스 그룹, Azure Cosmos DB 계정 및 모든 관련 리소스를 삭제해도 됩니다. 다음 단계에 따라 리소스 그룹을 삭제합니다.

 1. Azure Cosmos DB 계정을 만든 리소스 그룹으로 이동합니다.
 1. **리소스 그룹 삭제**를 선택합니다.
 1. 삭제할 리소스 그룹의 이름을 확인하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

자습서의 6부로 넘어가서 앱에 게시, 배치 및 삭제 함수를 추가합니다.

> [!div class="nextstepaction"]
> [6 부: 게시, 배치 및 삭제 함수를 앱에 추가](tutorial-develop-mongodb-nodejs-part6.md)
