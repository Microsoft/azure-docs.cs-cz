---
title: Nasazení rozhraní API služby Azure pro FHIR pomocí webu Azure Portal
description: Nasazení rozhraní API služby Azure pro FHIR pomocí webu Azure Portal.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 8699abfa8cc9b6675c15c8cd9b7cbb5bb5e148cd
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823471"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-portal"></a>Rychlý start: Nasazení rozhraní API služby Azure pro FHIR pomocí webu Azure portal

V tomto rychlém startu budete informace o nasazení rozhraní API služby Azure pro FHIR pomocí webu Azure Portal.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-new-resource"></a>Vytvořit nový prostředek

Otevřít [portálu Azure preview](https://preview.portal.azure.com) a klikněte na tlačítko **vytvořit prostředek**

![Vytvoření prostředku](media/quickstart-paas-portal/portal-create-resource.png)

## <a name="search-for-azure-api-for-fhir"></a>Hledat FHIR pro rozhraní API služby Azure

Rozhraní API služby Azure pro FHIR najdete tak, že do vyhledávacího pole zadáte "FHIR":

![Hledat rozhraní API pro zdravotnictví](media/quickstart-paas-portal/portal-search-healthcare-apis.png)

## <a name="create-azure-api-for-fhir-account"></a>Vytvoření rozhraní API služby Azure pro účet FHIR

Vyberte **vytvořit** k vytvoření nového rozhraní API Azure pro účet FHIR:

![Vytvoření rozhraní API služby Azure pro účet FHIR](media/quickstart-paas-portal/portal-create-healthcare-apis.png)

## <a name="enter-account-details"></a>Zadejte podrobnosti o účtu

Vyberte existující skupinu prostředků nebo vytvořte novou, zvolte název pro účet a nakonec klikněte na tlačítko **revize + vytvořit**:

![Podrobnosti nového rozhraní api zdravotní péče](media/quickstart-paas-portal/portal-new-healthcareapi-details.png)

Ověřte vytvoření a operátoru await FHIR rozhraní API nasazení.

## <a name="additional-settings"></a>Další nastavení

Klikněte na tlačítko **Další: Další nastavení** ke konfiguraci identity objektu ID, které by měl mít povolený přístup k tomuto rozhraní API Azure pro FHIR:

![Nakonfigurovat ID povolených objektů](media/quickstart-paas-portal/configure-allowed-oids.png)

Zobrazit [jak najít ID objektů identity](find-identity-object-ids.md) podrobnosti o tom, jak najít identitu objektu ID pro uživatele a instanční.

## <a name="fetch-fhir-api-capability-statement"></a>Načíst FHIR API funkce – příkaz

K ověření, že je zřízený nový účet FHIR API, načíst příkaz schopností najetím myší prohlížeč `https://<ACCOUNT-NAME>.azurehealthcareapis.com/metadata`.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, rozhraní API služby Azure pro FHIR a všechny související prostředky. Uděláte to tak, vyberte skupinu prostředků, který obsahuje rozhraní API služby Azure pro FHIR účtu, vyberte možnost **odstranit skupinu prostředků**, potvrďte název skupiny prostředků pro odstranění.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nasadili rozhraní API služby Azure pro FHIR do vašeho předplatného. Informace o přístup k rozhraní API FHIR pomocí nástroje Postman, pokračujte kurzem Postman.

>[!div class="nextstepaction"]
>[Používání rozhraní API FHIR pomocí nástroje Postman](access-fhir-postman-tutorial.md)