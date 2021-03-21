---
title: Izolace sítě
description: Uživatelé můžou omezit veřejný přístup k prostředkům QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: afb396bc364a2fa2db923fbcbe6bfe1b7aedbc26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467540"
---
# <a name="recommended-settings-for-network-isolation"></a>Doporučené nastavení pro izolaci sítě

Chcete-li omezit veřejný přístup k prostředkům QnA Maker, postupujte podle následujících kroků. Ochrana prostředku Cognitive Services před veřejným přístupem [konfigurací virtuální sítě](../../cognitive-services-virtual-networks.md?tabs=portal).

## <a name="restrict-access-to-app-service-qna-runtime"></a>Omezení přístupu k App Service (běhový modul QnA)

Můžete přidat IP adresy do seznamu povolených služeb App Service, abyste omezili přístup nebo nakonfigurovali App Service Environment k hostování QnA Maker App Service.

#### <a name="add-ips-to-app-service-allow-list"></a>Přidání IP adres do seznamu povolených App Service

1. Povoluje provoz jenom z Cognitive Servicesch IP adres. Tyto jsou již zahrnuty do tagu služby `CognitiveServicesManagement` . To se vyžaduje pro vytváření rozhraní API (vytvořit/aktualizovat KB) pro vyvolání služby App Service a aktualizaci služby Azure Search. Podívejte se na [Další informace o značkách služby.](../../../virtual-network/service-tags-overview.md)
2. Ujistěte se, že také povolíte další vstupní body, jako je Azure Bot Service, portál QnA Maker atd. pro předpověď "GenerateAnswer" přístup k rozhraní API.
3. Pokud chcete přidat rozsahy IP adres do seznamu povolených adres, postupujte prosím podle těchto kroků:

   1. Stáhněte si [rozsahy IP adres pro všechny značky služeb](https://www.microsoft.com/download/details.aspx?id=56519).
   2. Vyberte IP adresy "CognitiveServicesManagement".
   3. Přejděte do části sítě v prostředku App Service a kliknutím na možnost konfigurovat omezení přístupu přidejte IP adresy do seznamu povolených.

K dispozici je také automatizovaný skript, který bude pro váš App Service stejný. Pomocí [skriptu PowerShellu můžete nakonfigurovat seznam povolených](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) v GitHubu. Je potřeba zadat ID předplatného, skupinu prostředků a skutečný název App Service jako parametry skriptu. Po spuštění skriptu se IP adresy automaticky přidají do seznamu povolených App Service.

#### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>Konfigurace App Service Environment pro hostování QnA Maker App Service
    
K hostování služby QnA Maker App Service se dá použít App Service Environment (pomocného mechanismu). Postupujte následovně:

1. Vytvořte App Service Environment a označte ji jako "externí". Pokyny najdete v tomto [kurzu](../../../app-service/environment/create-external-ase.md) .
2.  Vytvořte službu App Service v rámci App Service Environment.
    1. Ověřte konfiguraci služby App Service a jako nastavení aplikace přidejte ' PrimaryEndpointKey '. Hodnota pro ' PrimaryEndpointKey ' by měla být nastavena na hodnotu \<app-name\> -PrimaryEndpointKey. Název aplikace je definovaný v adrese URL služby App Service. Pokud je například adresa URL služby App Service "mywebsite.myase.p.azurewebsite.net", pak název aplikace je "mywebsite". V tomto případě by měla být hodnota pro ' PrimaryEndpointKey ' nastavena na "mywebsite-PrimaryEndpointKey".
    2. Vytvořte službu Azure Search.
    3. Ujistěte se, že Azure Search a nastavení aplikace jsou správně nakonfigurované. 
          Postupujte prosím podle tohoto [kurzu](../reference-app-service.md?tabs=v1#app-service).
3.  Aktualizace skupiny zabezpečení sítě přidružené k App Service Environment
    1. Aktualizujte předem vytvořená příchozí pravidla zabezpečení podle vašich požadavků.
    2. Přidejte nové příchozí pravidlo zabezpečení se zdrojem jako značkou služby a označením zdrojové služby jako "CognitiveServicesManagement".
       
    ![výjimky portů pro příchozí spojení](../media/inbound-ports.png)

4.  Vytvořte instanci služby QnA Maker rozpoznávání (Microsoft. Cognitiveservices Account/Accounts) pomocí Azure Resource Manager, kde QnA Maker koncový bod by měl být nastaven na App Service koncový bod, který jste vytvořili výše (https://mywebsite.myase.p.azurewebsite.net).
    
---

## <a name="restrict-access-to-cognitive-search-resource"></a>Omezení přístupu k prostředku Kognitivní hledání

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

Kognitivní hledání instance se dá po vytvoření QnA Maker prostředků izolovat prostřednictvím privátního koncového bodu. Připojení privátního koncového bodu vyžadují virtuální síť, ke které je možné přistupovat instanci Search Service. 

Pokud je App Service QnA Maker omezený pomocí App Service Environment, použijte stejnou virtuální síť k vytvoření připojení privátního koncového bodu k instanci Kognitivní hledání. Vytvořte novou položku DNS ve virtuální síti pro mapování Kognitivní hledáního koncového bodu na IP adresu privátního koncového bodu Kognitivní hledání. 

Pokud se App Service Environment pro App Service Qnamakerem nepoužívá, nejprve vytvořte nový prostředek virtuální sítě a pak vytvořte připojení privátního koncového bodu k instanci Kognitivní hledání. V takovém případě musí být App Service QnA Maker [integrována s virtuální](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet) sítí, aby se mohla připojit k instanci kognitivní hledání. 

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

[Vytvořte privátní koncové body](../reference-private-endpoint.md) pro prostředek Azure Search.

---
