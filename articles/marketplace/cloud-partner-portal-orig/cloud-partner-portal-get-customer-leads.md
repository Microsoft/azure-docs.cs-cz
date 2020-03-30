---
title: Konfigurace zájemců zákazníků | Azure Marketplace
description: Konfigurace zájemců zákazníků na portálu partnerů cloudu.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 56012fb2a907a6db6f87554660ee36b99a3dcbf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280316"
---
<a name="get-customer-leads"></a>Získání potenciálních zákazníků
==================

Tento článek vysvětluje, jak vytvořit zákazníka zájemce pomocí portálu partnerů cloudu. Tyto potenciální zákazníky můžete připojit k crm systému a integrovat je do prodejního kanálu.

## <a name="leads"></a>Leads

Zájemci jsou zákazníci, kteří mají zájem nebo nasazují vaše produkty z [Azure Marketplace](https://azuremarketplace.microsoft.com/) nebo z [AppSource](https://appsource.microsoft.com).

### <a name="azure-marketplace"></a>Azure Marketplace

1.  Zákazník provede "Testovací jízdu" vaší nabídky. Testovací jízdy jsou zrychlenou příležitostí k okamžitému sdílení vaší firmy s potenciálními zákazníky bez překážek vstupu. Všechny testovací jednotky generují zájemce pro zákazníka, který má zájem vyzkoušet si váš produkt, aby se dozvěděl další informace. Další informace o testovacích discích na [Azure Marketplace Test Drive](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Příklady testovacích jednotek marketplace](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. Zákazník souhlasí se sdílením svých informací po výběru možnosti "Získat nyní". Tento zájemce je **počátečním zájemcem,** kde sdílíme informace o zákazníkovi, který projevil zájem o získání vašeho produktu. Zájemce je horní část akviziční cesty.

   ![Získat nyní možnost](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. Zákazník vybere "Nákup" na [webu Azure Portal,](https://portal.azure.com/) aby získal váš produkt. Tento zájemce je **aktivním** zájemcem, kde sdílíme informace o zákazníkovi, který začal nasazovat váš produkt.

   ![Možnost nákupu](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  Zákazník vzal "Test Drive" pro vaši nabídku. Testovací jízdy jsou zrychlenou příležitostí k okamžitému sdílení vaší firmy s potenciálními zákazníky bez překážek vstupu. Všechny testovací jízdy vygenerují zájem zákazníka, který má zájem vyzkoušet si váš produkt, aby se dozvěděl další informace. Další informace o testovacích discích na [AppSource Test Drive](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Příklad testovací jízdy](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  Zákazník souhlasí se sdílením svých informací po výběru možnosti "Získat nyní". Tento zájemce je **počátečním zájemcem,** kde sdílíme informace o zákazníkovi, který vyjadřuje zájem o získání vašeho produktu. Zájemce je horní část akviziční cesty.

      ![Získat nyní možnost](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  Zákazník vybere "Kontaktujte mě" na vaší nabídce. Tento zájemce je **aktivním** zájemcem, kde sdílíme informace o zákazníkovi, který žádá o další informace o vašem produktu.

    ![Možnost Kontaktovat mě](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Data zájemce
---------

Každý zájemce, který obdržíte během procesu získávání zákazníků, má data v konkrétních polích. Vzhledem k tomu, že zájemce získáte z několika kroků, nejlepším způsobem, jak s zájemci zacházet, je duplikovat a přizpůsobit následná opatření. Tímto způsobem každý zákazník dostává příslušnou zprávu a vytváříte jedinečný vztah.

### <a name="lead-source"></a>Zdroj zájemců

Formát zdroje zájemců je**Nabídka** **zdrojové**-**akce** |  

**Zdroje:**"AzureMarketplace", "AzurePortal", "TestDrive" a "AppSource (SPZA)"

**Akce**:
- "INS" - instalace. Tato akce se provádí na Azure Marketplace nebo AppSource, když si zákazník koupí váš produkt.
- "PLT" - Zkratka pro Partner Led Trial. Tato akce je na AppSource, když zákazník používá možnost Kontaktujte mě.
- "DNC" - Nekontaktujte. Tato akce se na AppSource, když partner, který byl kříž uvedený na stránce aplikace dostane požádán, aby byl kontaktován. Sdílíme informace o tom, že tento zákazník byl ve vaší aplikaci uveden na kříži, ale není třeba ho kontaktovat.
- "Vytvořit" – Tato akce je jenom uvnitř portálu Azure Portal a je generována, když zákazník zakoupí vaši nabídku na svůj účet.
- "StartTestDrive" – Tato akce je pouze pro testovací jednotky a je generována, když zákazník spustí testovací jednotku.

**Nabídky**

Následující příklady ukazují jedinečné identifikátory, které jsou přiřazeny vydavateli a konkrétní nabídku: checkpoint.check-point-r77-10sg-byol, bitnami.openedxcypress a docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Informace o zákaznících

Pole v následujícím příkladu zobrazují informace o zákazníkovi obsažené v zájemci.
- Jméno: Jan
- Příjmení: Novák
- E-mail:\@jsmith microsoft.com
- Telefon: 1234567890
- Země: USA
- Společnost: Microsoft
- Název: CTO

>[!Note]
>Pro každého zájemce nejsou vždy k dispozici všechna data v předchozím příkladu.

Aktivně pracujeme na vylepšování potenciálních zákazníků, takže pokud existuje datové pole, které zde nevidíte, ale chcete mít, [zašlete nám prosím svůj názor](mailto:AzureMarketOnboard@microsoft.com).

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Jak propojit CRM systém s portálem cloudových partnerů
------------------------------------------------------------

Abychom začali dostávat potenciální zákazníky, vytvořili jsme náš konektor pro správu potenciálních zákazníků na portálu partnerů cloudu, abyste mohli snadno připojit informace o CRM a my vám toto připojení nastavíme. Nyní můžete snadno využít potenciální zákazníky generované na trhu bez významného technického úsilí o integraci s externím systémem.

![Konektor pro správu potenciálních zákazníků](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Zájemce můžeme zapsat do různých crm systémů nebo přímo do tabulky úložišť Azure, kde můžete zájemce spravovat, jak chcete. Každý z následujících odkazů obsahuje pokyny pro připojení k možným cílům potenciálních zákazníků:

-   [Dynamics CRM Online,](./cloud-partner-portal-lead-management-instructions-dynamics.md) kde najdete pokyny ke konfiguraci aplikace Dynamics CRM Online pro získávání zájemců.
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) získat pokyny pro nastavení Marketo Olovo konfigurace získat vede.
-    [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md) získat pokyny pro nastavení instance Salesforce získat zájemce.
-    [Azure Table,](./cloud-partner-portal-lead-management-instructions-azure-table.md) abyste získali pokyny pro nastavení účtu úložiště Azure pro získání zájemců v tabulce Azure.
-   [Https Koncový bod](./cloud-partner-portal-lead-management-instructions-https.md) získat pokyny pro nastavení https koncového bodu získat zájemce.

Po konfiguraci cíle zájemce a publikování nabídky ověříme připojení a zašleme vám testovacího zájemce. Když si nabídku prohlížíte před tím, než začnete žít, můžete také otestovat připojení zájemců tím, že se pokusíte získat nabídku sami v prostředí náhledu. Je důležité zajistit, aby nastavení zájemců zůstalo aktuální, abyste nepřišli o žádné potenciální zákazníky, proto se ujistěte, že tato připojení aktualizujete vždy, když se na vaší straně něco změní.

<a name="what-next"></a>Co dál?
----------

Jakmile je technické nastavení zavedeno, měli byste tyto zájemce začlenit do aktuální prodejní & marketingové strategie a provozních procesů. Máme velký zájem o lepší pochopení vašeho celkového prodejního procesu a chceme s vámi úzce spolupracovat na poskytování vysoce kvalitních potenciálních zákazníků a dostatečného množství dat, abyste byli úspěšní. Uvítáme vaši zpětnou vazbu o tom, jak můžeme optimalizovat a vylepšovat potenciální zákazníky, které vám posíláme, s dalšími údaji, které pomohou těmto zákazníkům uspět. Pokud máte zájem o poskytnutí [zpětné vazby](mailto:AzureMarketOnboard@microsoft.com) a návrhů, abyste mohli být úspěšnější s marketplace zájemci, dejte nám vědět.
