---
title: Dynamics 365 Customer Engagement aplikace nabízí karta Test Drive – Azure Marketplace | Dokumentace Microsoftu
description: Jak konfigurovat testovací verze pro Dynamics 365 Customer Engagement aplikace nabídky na webu AppSource Marketplace.
services: Azure, Marketplace, AppSource, Cloud Partner Portal, Dynamics 365 for Customer Engagement
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/25/2018
ms.author: pbutlerm
ms.openlocfilehash: 664a2c6bfc4a73b7d792b71c4b81df54b05fcd74
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082689"
---
# <a name="dynamics-365-for-customer-engagement-application-test-drive-tab"></a>Dynamics 365 Customer Engagement aplikaci Test Drive karta

Použití **Test Drive** kartu a vytvořte zkušební prostředí pro vaše zákazníky.  Zákazníkům přináší praktická, samostatně prováděného zkušební verzi klíčové funkce a výhody, které jsme vám ukázali ve scénáři Skutečná implementace vaší nabídky.  Možnosti zkušebních verzí dostupné Test Drive je nejúčinnější pro vysoce kvalitní generování potenciálních zákazníků a větší převodu těchto potenciálních zákazníků.  Další informace najdete v tématu [co je to Test Drive?](../../cloud-partner-portal-orig/what-is-test-drive.md)

Testovací verze prostředí pro aplikace Dynamics 365 se automaticky spustí jako řešení hostovaných Microsoftem.  Další informace najdete v tématu [hostovaná testovací verze](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/hosted-test-drive).

Na kartě testovací verze má tři části potenciální: **Vyzkoušejte si**, **podrobnosti**, a **technické konfigurace**.  Poslední dvě části se zobrazí pouze po povolení funkce testovací verze.  Hvězdičku (*) připojeným k názvu pole označuje, že je to požadováno. 


## <a name="test-drive-section"></a>Test jednotky oddílu

Chcete-li tuto funkci povolit, vyberte **Ano** k **povolit si testovací jízdu**.


## <a name="details-section"></a>V části Podrobnosti o

Bude zadání základních informací testovací jízdu v **podrobnosti** oddílu.   

![Části Podrobné informace o testovací verze](./media/test-drive-tab-details.png)

Následující tabulka popisuje pole k nastavení testovací verze pro vaši aplikaci Dynamics 365.

|      Pole                    |    Popis                  |
|    ---------                  |  ---------------                |
|      Popis              |   Popište, co se dá dělat na vaše testovací verze. Základní značky HTML můžete použít k formátování tento popis. Například &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;a záhlaví.  |
|  Uživatelská příručka                  |   Nahrajte uživatelská příručka pro vaši zákazníci můžou použít pro vás provedl možnostmi, testovací verze. Tento dokument musí být soubor PDF.              |
|  Test jednotky – ukázkové Video (volitelné) |  Můžete zadat video s návodem, vaše testovací verze. Zákazník může sledovat toto video předtím, než si testovací jízdu. Zadejte URL adresu videa na YouTube nebo Vimeo. Pokud vyberete **+ přidat Video**, budete vyzváni k zadání následujících informací:<ul><li>Název</li><li>zprostředkovatele identity</li><li>Miniatura (ve formátu PNG, 533 x 324 pixelů)</li></ul>  |
|   |   |


## <a name="technical-configuration-section"></a>Technické konfiguračního oddílu

v této části zadáte podrobnosti o vaše testovací verze.

![Části Podrobné informace o testovací verze](./media/test-drive-tab-tech-config.png)

Kde pole mají k těmto účelům:

|      Pole                    |    Popis                  |
|    ---------                  |  ---------------                |
| Typ testu jednotky            | Zvolte **Microsoft hostované (Dynamics 365 for Customer Engagement)**.  |
| Maximálního počtu současných testovací verze    | Počet souběžných instancí aktivní testovací verze v libovolném časovém okamžiku dobu. Každý uživatel bude využívat licence Dynamics, jejich Test Drive je aktivní, proto budete muset zajistit, že máte alespoň tolik Dynamics licencí k dispozici pro uživatele testovací verze. Doporučená hodnota 3 – 5.  |
| Jednotka doba trvání testu (hodiny)   | Maximální počet hodin, po které budou aktivní pro instanci testovací verze daného uživatele. Po překročení tohoto období budou instance zrušit z vašeho tenanta. Doporučená hodnota 2 až 24 hodin v závislosti na složitosti vaší aplikace. Je-li spustit mimo časový limit a chcete znovu vyhodnotit, může uživatel vždy požádat jiného testovací verze.  |
| Adresa URL instance                  | Adresa URL, které testovací verze se nejprve přejděte do. Toto je obvykle adresa URL instance Dynamics 365, který má vaše aplikace a ukázková data nainstalována.  |
| ID klienta Azure AD            | Identifikátor GUID klienta Azure pro vaši instanci Dynamics 365. Načíst tuto hodnotu, přihlaste se k webu Azure portal a přejděte do **Azure Active Directory** > **vybrat vlastnosti** > **zkopírujte ID adresáře sady**.  |
| ID aplikace Azure AD               | Identifikátor GUID aplikace Azure AD  |
| Klíč aplikace Azure AD              | Tajný kód aplikace Azure AD, například: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` |
| Název Tenanta Azure AD          | Název tenanta Azure pro vaši instanci Dynamics 365. Použijte formát < tenantname. > onmicrosoft.com, například: `testdrive.onmicrosoft.com`  |
| Adresa URL instance webové rozhraní API          | Adresa URL webového rozhraní API pro vaši instanci Dynamics 365. Tuto hodnotu můžete načíst tak, že přihlášení k vaší instanci Microsoft Dynamics 365 a přejdete na **nastavení** > **přizpůsobení** > **pro vývojáře Prostředky** > **Instance webové rozhraní API (zkopírujte tuto adresu URL)**. Příklad hodnoty: `https://testdrive.crm.dynamics.com/api/data/v9.0`  |
| Název role                     | Název vlastní role zabezpečení Dynamics 365 vytvořili pro vaše testovací verze a přiřadí uživatelům při spuštění, například `testdriveuser`. |
|  |  |

Po zadání všech požadovaných informací, vyberte **Uložit**.


## <a name="next-steps"></a>Další postup

Dále bude obsahovat informace marketingu a prodeje v [kartu Podrobnosti z prodejních míst](./cpp-storefront-details-tab.md).

