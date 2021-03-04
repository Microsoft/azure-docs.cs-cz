---
title: Izolace sítě
description: Uživatelé můžou omezit veřejný přístup k prostředkům QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 4e9b45c72e5fb4cbd9e548727faf8946e3e5ba8f
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102125055"
---
# <a name="recommended-settings-for-network-isolation"></a>Doporučené nastavení pro izolaci sítě

Chcete-li omezit veřejný přístup k prostředkům QnA Maker, postupujte podle následujících kroků. Ochrana prostředku Cognitive Services před veřejným přístupem [konfigurací virtuální sítě](../../cognitive-services-virtual-networks.md?tabs=portal).

## <a name="restrict-access-to-cognitive-search-resource"></a>Omezení přístupu k prostředku Kognitivní hledání

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

Konfigurace Kognitivní hledání privátního koncového bodu v rámci virtuální sítě. Když se vytvoří instance hledání během vytváření prostředku QnA Maker, můžete vynutit Kognitivní hledání pro podporu konfigurace privátního koncového bodu, která se zcela vytvořila v rámci virtuální sítě zákazníka.

Pro použití privátního koncového bodu musí být všechny prostředky vytvořeny ve stejné oblasti.

* Prostředek QnA Maker
* nový prostředek Kognitivní hledání
* nový prostředek Virtual Network

V [Azure Portal](https://portal.azure.com)proveďte následující kroky:

1. Vytvořte [prostředek QnA maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker).
2. Vytvořte nový prostředek Kognitivní hledání s možností připojení koncových bodů (data) nastavenou na _Private_. Vytvořte prostředek ve stejné oblasti, jako je QnA Maker prostředek vytvořený v kroku 1. Přečtěte si další informace o [vytvoření prostředku kognitivní hledání](../../../search/search-create-service-portal.md)a pak použijte tento odkaz k přechodu přímo na [stránku vytváření prostředku](https://ms.portal.azure.com/#create/Microsoft.Search).
3. Vytvořte nový [prostředek Virtual Network](https://ms.portal.azure.com/#create/Microsoft.VirtualNetwork-ARM).
4. Nakonfigurujte virtuální síť u prostředku služby App Service vytvořeného v kroku 1 tohoto postupu. Vytvořte novou položku DNS ve virtuální síti pro nový prostředek Kognitivní hledání vytvořený v kroku 2. do Kognitivní hledání IP adresa.
5. [Přidružte službu App Service k novému kognitivní hledání prostředku](../how-to/set-up-qnamaker-service-azure.md) vytvořenému v kroku 2. Pak můžete odstranit původní prostředek Kognitivní hledání vytvořený v kroku 1.
    
Na [portálu QnA maker](https://www.qnamaker.ai/)vytvořte svou první znalostní bázi.

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

[Vytvořte privátní koncové body](../reference-private-endpoint.md) pro prostředek Azure Search.

---

## <a name="restrict-access-to-app-service-qna-runtime"></a>Omezení přístupu k App Service (běhový modul QnA)

Do App Service povolených můžete přidat IP adresy, abyste omezili přístup nebo nakonfigurovali App Service Environemnt na hostování QnA Maker App Service.

#### <a name="add-ips-to-app-service-allowlist"></a>Přidání IP adres do App Service povolených

1. Povoluje provoz jenom z Cognitive Servicesch IP adres. Tyto jsou již zahrnuty do tagu služby `CognitiveServicesManagement` . To se vyžaduje pro vytváření rozhraní API (vytvořit/aktualizovat KB) pro vyvolání služby App Service a aktualizaci služby Azure Search. Podívejte se na [Další informace o značkách služby.](../../../virtual-network/service-tags-overview.md)
2. Ujistěte se, že také povolíte další vstupní body, jako je Azure Bot Service, portál QnA Maker atd. pro předpověď "GenerateAnswer" přístup k rozhraní API.
3. Pokud chcete přidat rozsahy IP adres do povolenýchu, postupujte prosím podle těchto kroků:

   1. Stáhněte si [rozsahy IP adres pro všechny značky služeb](https://www.microsoft.com/download/details.aspx?id=56519).
   2. Vyberte IP adresy "CognitiveServicesManagement".
   3. Přejděte do části sítě v prostředku App Service a kliknutím na možnost konfigurovat omezení přístupu přidejte IP adresy do povolených.

K dispozici je také automatizovaný skript, který bude pro váš App Service stejný. [Skript PowerShellu můžete nakonfigurovat tak, aby povolených](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) na GitHubu. Je potřeba zadat ID předplatného, skupinu prostředků a skutečný název App Service jako parametry skriptu. Po spuštění skriptu se tyto IP adresy automaticky přidají App Service povolených.

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
4.  Vytvořte instanci služby QnA Maker rozpoznávání (Microsoft. Cognitiveservices Account/Accounts) pomocí Azure Resource Manager, kde QnA Maker koncový bod by měl být nastaven na App Service koncový bod, který jste vytvořili výše (https://mywebsite.myase.p.azurewebsite.net).
    
---
