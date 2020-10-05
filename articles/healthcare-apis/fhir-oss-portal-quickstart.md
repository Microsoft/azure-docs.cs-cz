---
title: 'Azure Portal: nasazení Open Source serveru FHIR pro Azure – Azure API pro FHIR'
description: V tomto rychlém startu se dozvíte, jak nasadit server Microsoft Open Source FHIR pomocí Azure Portal.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 57ab6bca820c4c25a9a56e4a801aa7d917d317ec
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90978598"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>Rychlý Start: nasazení Open Source serveru FHIR pomocí Azure Portal

V tomto rychlém startu se dozvíte, jak nasadit Open Source Server FHIR v Azure pomocí Azure Portal. V [úložišti Open Source](https://github.com/Microsoft/fhir-server) budeme používat odkazy pro snadné nasazení.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="github-open-source-repository"></a>Úložiště GitHub Open Source

Přejděte na [stránku nasazení GitHubu](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md) a vyhledejte tlačítka nasadit do Azure:

>[!div class="mx-imgBorder"]
>![Stránka nasazení Open Source](media/quickstart-oss-portal/deployment-page-oss.png)

Klikněte na tlačítko nasazení a otevře se Azure Portal.

## <a name="fill-in-deployment-parameters"></a>Vyplnit parametry nasazení

Vyberte vytvořit novou skupinu prostředků a pojmenujte ji. Pouze další požadované parametry jsou název služby a heslo správce SQL.

>[!div class="mx-imgBorder"]
>![Parametry vlastního nasazení](media/quickstart-oss-portal/deployment-custom-parameters.png)

Po vyplnění podrobností můžete nasazení spustit.

## <a name="validate-fhir-server-is-running"></a>Ověřit, jestli je server FHIR spuštěný

Až se nasazení dokončí, můžete odkazovat na prohlížeč, aby `https://SERVICENAME.azurewebsites.net/metadata` se získal příkaz funkce. Může to chvíli trvat, než se server poprvé odpoví.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků, která obsahuje zřízené prostředky, vyberete **Odstranit skupinu prostředků**a pak ověříte název skupiny prostředků, která se má odstranit.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili server Microsoft Open Source FHIR pro Azure do svého předplatného. Pokud se chcete dozvědět, jak získat přístup k rozhraní FHIR API pomocí post, přejděte do kurzu post.
 
>[!div class="nextstepaction"]
>[Přístup k rozhraní FHIR API pomocí post](access-fhir-postman-tutorial.md)