---
title: Nasazení serveru otevřete FHIR zdroj pro Azure pomocí webu Azure Portal – rozhraní API od Microsoftu zdravotní péče
description: V tomto rychlém startu vysvětluje, jak nasadit server Microsoft otevřít zdroj FHIR pomocí webu Azure Portal.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 2bd7a7d0e332d281a0ca9a9017219b50a3fbb472
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823466"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>Rychlý start: Nasazení serveru otevřený zdroj FHIR pomocí webu Azure portal

V tomto rychlém startu budete informace o nasazení otevřete zdrojový FHIR Server v Azure pomocí webu Azure portal. Budeme používat odkazy snadné nasazení v [úložiště Open Source](https://github.com/Microsoft/fhir-server)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="github-open-source-repository"></a>Úložiště Open Source na Githubu

Přejděte [stránku Githubu nasazení](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md) a vyhledejte "Nasazení do Azure" tlačítka:

![Stránka nasazení Open Source](media/quickstart-oss-portal/deployment-page-oss.png)

Klikněte na tlačítko nasazení a otevře na portálu Azure portal.

## <a name="fill-in-deployment-parameters"></a>Vyplňte parametry nasazení

Zvolte možnost vytvořit novou skupinu prostředků a pojmenujte ho. Další požadovaný parametr je název služby.

![Nasazení vlastní parametry](media/quickstart-oss-portal/deployment-custom-parameters.png)

Všimněte si, že nasazení přetáhne zdrojový kód přímo z úložiště open source na Githubu. Pokud máte Rozvětvená úložiště, může odkazovat na své vlastní pro konkrétní větve.

Po vyplnění podrobnosti, můžete spustit nasazení.

## <a name="validate-fhir-server-is-running"></a>Ověření FHIR Server je spuštěn.

Po dokončení nasazení může odkazovat v prohlížeči `https://SERVICENAME.azurewebsites.net/metadata` získat možnosti příkazu. Bude trvat minutu nebo tak, aby server mohl reagovat prvním.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků a všechny související prostředky. Uděláte to tak, vyberte skupinu prostředků obsahující zřízené prostředky, vyberte **odstranit skupinu prostředků**, potvrďte název skupiny prostředků pro odstranění.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nasadili FHIR serveru otevřete zdroj Microsoft Azure do vašeho předplatného. Informace o přístup k rozhraní API FHIR pomocí nástroje Postman, pokračujte kurzem Postman.
 
>[!div class="nextstepaction"]
>[Používání rozhraní API FHIR pomocí nástroje Postman](access-fhir-postman-tutorial.md)