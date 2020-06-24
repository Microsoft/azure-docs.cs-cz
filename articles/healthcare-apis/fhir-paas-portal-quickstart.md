---
title: 'Rychlý Start: nasazení rozhraní Azure API pro FHIR pomocí Azure Portal'
description: V tomto rychlém startu se dozvíte, jak nasadit rozhraní API Azure pro FHIR a nakonfigurovat nastavení pomocí Azure Portal.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: e729597e9d83c4e6096fe52b577b052d94ca4799
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "84820250"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-portal"></a>Rychlý Start: nasazení rozhraní Azure API pro FHIR pomocí Azure Portal

V tomto rychlém startu se dozvíte, jak nasadit rozhraní API Azure pro FHIR pomocí Azure Portal.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="create-new-resource"></a>Vytvořit nový prostředek

Otevřete [Azure Portal](https://portal.azure.com) a klikněte na **vytvořit prostředek** .

![Vytvoření prostředku](media/quickstart-paas-portal/portal-create-resource.png)

## <a name="search-for-azure-api-for-fhir"></a>Hledání rozhraní API Azure pro FHIR

Rozhraní Azure API pro FHIR můžete najít zadáním "FHIR" do vyhledávacího pole:

![Hledání rozhraní API pro zdravotní péči](media/quickstart-paas-portal/portal-search-healthcare-apis.png)

## <a name="create-azure-api-for-fhir-account"></a>Vytvoření Azure API pro účet FHIR

Vyberte **vytvořit** a vytvořte nový Azure API pro účet FHIR:

![Vytvoření Azure API pro účet FHIR](media/quickstart-paas-portal/portal-create-healthcare-apis.png)

## <a name="enter-account-details"></a>Zadejte podrobnosti účtu.

Vyberte existující skupinu prostředků nebo vytvořte novou, zvolte název účtu a nakonec klikněte na **zkontrolovat + vytvořit**:

![Nové podrobnosti o rozhraní API pro zdravotnictví](media/quickstart-paas-portal/portal-new-healthcareapi-details.png)

Potvrďte vytváření a očekává se nasazení rozhraní API FHIR.

## <a name="additional-settings"></a>Další nastavení

Klikněte na **Další: Další nastavení** pro konfiguraci autority, cílové skupiny, ID objektů identity, která mají mít povolený přístup k tomuto rozhraní Azure API pro FHIR, v případě potřeby povolte inteligentní v FHIR a nakonfigurujte propustnost databáze:

- **Autorita:** Můžete zadat jiného tenanta Azure AD z rozhraní, které jste přihlášeni jako ověřovací autoritu pro službu.
- **Cílová skupina:** Osvědčeným postupem a výchozím nastavením je, že cílová skupina je nastavená na adresu URL FHIR serveru. Můžete to změnit tady. Cílová skupina identifikuje příjemce, pro kterého je token určen. V tomto kontextu by měl být nastaven na něco, co představuje samotný FHIR API.
- **Povolená ID objektů:** Můžete zadat ID objektů identity, která by měla mít povolený přístup k tomuto rozhraní Azure API pro FHIR. Další informace o tom, jak vyhledat ID objektu pro uživatele a instanční objekty, najdete v tématu Průvodce [hledáním objektů identity s ID](find-identity-object-ids.md) .  
- **Inteligentní na FHIR proxy serveru:** Můžete povolit SMART on FHIR proxy. Podrobnosti o tom, jak nakonfigurovat SMART on FHIR proxy, najdete v tématu kurz [Azure API pro FHIR Smart on FHIR proxy](https://docs.microsoft.com/azure/healthcare-apis/use-smart-on-fhir-proxy) .  
- **Zajištěná propustnost (ru/s):** Tady můžete zadat nastavení propustnosti pro podkladovou databázi pro Azure API pro FHIR. Toto nastavení můžete později změnit v okně databáze. Další podrobnosti najdete na stránce [Konfigurace nastavení databáze](configure-database.md) .


![Konfigurace povolených ID objektů](media/quickstart-paas-portal/configure-audience.png)

## <a name="fetch-fhir-api-capability-statement"></a>Načíst příkaz schopnosti rozhraní API FHIR

Pokud chcete ověřit, jestli je nový účet FHIR API zřízený, načtěte příkaz funkce tak, že přejdete na prohlížeč `https://<ACCOUNT-NAME>.azurehealthcareapis.com/metadata` .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete odstranit skupinu prostředků, Azure API pro FHIR a všechny související prostředky. Provedete to tak, že vyberete skupinu prostředků obsahující Azure API pro účet FHIR, vyberete **Odstranit skupinu prostředků**a pak ověříte název skupiny prostředků, která se má odstranit.

## <a name="next-steps"></a>Další kroky

V této příručce pro rychlý Start jste nasadili rozhraní Azure API pro FHIR do svého předplatného. Pokud chcete nastavit další nastavení v rozhraní API Azure pro FHIR, přejděte k Průvodci dalšími nastaveními.

>[!div class="nextstepaction"]
>[Další nastavení v rozhraní API Azure pro FHIR](azure-api-for-fhir-additional-settings.md)
