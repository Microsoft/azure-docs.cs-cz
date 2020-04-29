---
title: Konfigurace zákaznických zákazníků | Azure Marketplace
description: Nakonfigurujte potenciální zákazníky v portál partnerů cloudu.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 56012fb2a907a6db6f87554660ee36b99a3dcbf9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280316"
---
<a name="get-customer-leads"></a>Získání potenciálních zákazníků
==================

Tento článek vysvětluje, jak vytvořit zájemce zákazníka pomocí portál partnerů cloudu. Tyto zájemce můžete propojit s vaším systémem CRM a integrovat je do svého obchodního kanálu.

## <a name="leads"></a>Leads

Zájemci jsou zákazníci, kteří mají zájem, nebo nasazují vaše produkty z [Azure Marketplace](https://azuremarketplace.microsoft.com/) nebo z [AppSource](https://appsource.microsoft.com).

### <a name="azure-marketplace"></a>Azure Marketplace

1.  Zákazník vezme "zkušební verzi" vaší nabídky. Testovací jednotky představují urychlenou příležitost pro okamžité sdílení vaší firmy s potenciálními zákazníky bez jakýchkoli překážek vstupu. Všechny testovací jednotky vygenerují zájemce pro zákazníky, kteří mají zájem o vyzkoušení produktu a další informace. Přečtěte si další informace o testovacích jednotkách na [Azure Marketplace testovacím disku](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Příklady testovacích jednotek Marketplace](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. Zákazník souhlasí s sdílením svých informací po výběru možnosti "získat IT nyní". Tento potenciální zákazník je **počátečním zájmem** , kde sdílíme informace o zákazníkovi, který vyjádřil zájem o získání vašeho produktu. Vedoucí je horní část trychtýře pořízení.

   ![Získat možnost hned](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. Zákazník vybere možnost koupit na webu [Azure Portal](https://portal.azure.com/) a získá svůj produkt. Tento potenciální zákazník je **aktivním** zájemcem, který sdílí informace o zákazníkovi, který zahájil nasazení vašeho produktu.

   ![Možnost nákupu](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  Zákazník si pro vaši nabídku vybral "testovací disk". Testovací jednotky představují urychlenou příležitost pro okamžité sdílení vaší firmy s potenciálními zákazníky bez jakýchkoli překážek vstupu. Všechny testovací jednotky vygenerují potenciálního zákazníka, který vás zajímá při pokusu o vyzkoušení vašeho produktu a další informace. Přečtěte si další informace o testovacích jednotkách na [AppSource testovacím disku](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Příklad testovací jednotky](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  Zákazník souhlasí s sdílením svých informací po výběru možnosti "získat IT nyní". Tento potenciální zákazník je **počátečním zájmem** , kde sdílíme informace o zákazníkovi, který vyjadřuje zájem o získání vašeho produktu. Vedoucí je horní část trychtýře pořízení.

      ![Získat možnost hned](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  Zákazník vybere u vaší nabídky "kontaktujte mě". Tento potenciální zákazník je **aktivním** zájemcem, který sdílí informace o zákazníkovi, který požaduje, aby se s produktem řídili.

    ![Kontakt mi – možnost](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Data potenciálních zákazníků
---------

Každý zájemce, kterého obdržíte během procesu pořízení zákazníka, má data v konkrétních polích. Vzhledem k tomu, že dostanete zájemce z více kroků, nejlepším způsobem, jak tyto zájemce zpracovat, je zrušit duplicitu a přizpůsobit následné. Tímto způsobem si jednotliví zákazníci dostanou příslušnou zprávu a vytváříte jedinečnou relaci.

### <a name="lead-source"></a>Zdroj potenciálních zákazníků

Formát pro zdroj potenciálního zákazníka je**Nabídka** **zdrojové**-**Akce** |  .

**Zdroje**: "AzureMarketplace", "AzurePortal", "testdrive" a "APPSOURCE (SPZA)"

**Akce**:
- "INS" – instalace. Tato akce se provádí v Azure Marketplace nebo AppSource, když zákazník koupí váš produkt.
- "PLT" – představuje zkušební verzi LED pro partnery. Tato akce se provádí v AppSource, když zákazník používá možnost kontakt mi.
- "DNC" – nekontaktuje se. Tato akce se provádí na AppSource, když se partner, který jste provedli na stránce aplikace, vyžádá, aby kontaktoval. Sdílíme, že tento zákazník byl ve vaší aplikaci uveden výše, ale není potřeba ho kontaktovat.
- "Vytvořit" – Tato akce je určena pouze v rámci webu Azure Portal a je generována, když zákazník koupí vaši nabídku na svůj účet.
- "StartTestDrive" – Tato akce je určena pouze pro testovací jednotky a je generována, když zákazník spustí testovací jednotku.

**Nabídky**

Následující příklady znázorňují jedinečné identifikátory, které jsou přiřazeny vydavateli a konkrétní nabídce: Checkpoint. Check-Point-r77-10sg-BYOL, Bitnami. openedxcypress a Docusign. 3701c77e-1cfa-4c56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Informace o zákazníkovi

Pole v následujícím příkladu ukazují informace o zákaznících, které jsou obsaženy v rámci zájemce.
- FirstName: Jan
- LastName: Smith
- E-mail: JSmith\@Microsoft.com
- Telefon: 1234567890
- Země: US
- Společnost: Microsoft
- Název: technický ředitel

>[!Note]
>Ne všechna data v předchozím příkladu jsou vždy k dispozici pro každého zájemce.

Aktivně pracujeme na vylepšení zájemců, takže pokud existuje datové pole, které tady nevidíte, ale chcete, [pošlete nám](mailto:AzureMarketOnboard@microsoft.com)prosím svůj názor.

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Postup připojení systému CRM pomocí portál partnerů cloudu
------------------------------------------------------------

Abychom mohli začít s přijímáním zájemců, sestavili jsme náš konektor pro správu potenciálních zákazníků na portál partnerů cloudu, abyste mohli snadno připojit informace o CRM a my vám budeme připojení. Teď můžete snadno využít potenciální zákazníky vygenerované na webu Marketplace bez významného vývojového úsilí pro integraci s externím systémem.

![Konektor pro správu potenciálních zákazníků](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Zájemce můžeme psát do celé řady systémů CRM nebo přímo do Azure Storage tabulky, kde můžete spravovat potenciální zákazníky. Každý z následujících odkazů obsahuje pokyny pro připojení k možným cílům potenciálních zákazníků:

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) vám umožní získat pokyny ke konfiguraci Dynamics CRM online za účelem získání zájemců.
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) , kde najdete pokyny k nastavení konfigurace vedoucího služby Marketo k získání zájemců.
-    [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md) , kde najdete pokyny k nastavení instance Salesforce pro získání potenciálních zákazníků.
-    [Tabulka Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md) , kde najdete pokyny k nastavení účtu Azure Storage pro získávání zájemců v tabulce Azure.
-   [Koncovým bodem https](./cloud-partner-portal-lead-management-instructions-https.md) získáte pokyny k nastavení koncového bodu https, abyste získali zájemci.

Po nakonfigurování cíle realizace a publikování vaší nabídky ověříme připojení a pošleme vám testovacího vedoucího. Při prohlížení nabídky před tím, než budete připraveni, můžete také otestovat své zájemce tím, že se pokusíte získat nabídku sami v prostředí verze Preview. Je důležité zajistit, aby vaše nastavení potenciálních zákazníků zůstalo v aktuálním stavu, takže nepřijdete o žádné zájemce, proto nezapomeňte aktualizovat tato připojení, kdykoli se na konci změnila nějaká změna.

<a name="what-next"></a>Co dál?
----------

Až bude technická instalace zavedena, měli byste tyto zájemce začlenit do své aktuální prodejní & strategie a provozní procesy. Máme na vědomí lepší přehled o celkovém prodejním procesu a poznáte s vámi, abyste měli k dispozici vysoce kvalitní zájem a dostatečné množství dat, abychom vám tak mohli úspěšně probíhat. Uvítáme vaše názory na to, jak můžeme optimalizovat a zdokonalovat zájemce, abychom vám poslali další data, abychom těmto zákazníkům pomohli zajistit úspěch. Dejte nám prosím vědět, pokud máte zájem o [poskytování zpětné vazby](mailto:AzureMarketOnboard@microsoft.com) a návrhů, které umožní vašemu prodejnímu týmu získat větší úspěšnost s zájemci na webu Marketplace.
