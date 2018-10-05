---
title: Konfigurace potenciálních zákazníků | Dokumentace Microsoftu
description: Nakonfigurujte potenciálních zákazníků v portál partnerů cloudu.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 997727acd1bbaf17577160bb996aad7e21d49c86
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809264"
---
<a name="get-customer-leads"></a>Získání potenciálních zákazníků
==================

Tento článek vysvětluje, jak vytvořit pomocí portálu pro partnery Cloud potenciálních zákazníků. Můžete připojit k systému CRM těchto potenciálních zákazníků a integrovat do prodejních kanálů.

## <a name="leads"></a>Potenciálních zákazníků

Potenciální zákazníci jsou zákazníci, kteří mají zájem o nebo své produkty z nasazení [Azure Marketplace](https://azuremarketplace.microsoft.com/) nebo z [AppSource](https://appsource.microsoft.com).

### <a name="azure-marketplace"></a>Azure Marketplace

1.  Zákazník má "Jednotka testu" o vaší nabídce. Testovací verze jsou akcelerované příležitost, můžete okamžitě sdílet vaši firmu s potenciálními zákazníky bez jakékoli překážky k položce. Všechny testovací verze generovat zájemce pro zákazníky, kteří se zajímají pokusu o produktu Další informace. Další informace o Test jednotek při [vyzkoušejte Azure Marketplace](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Příklady Marketplace testů jednotky](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

2.  Zákazník souhlasí s sdílení svých informací po výběru "Stáhnout". Je toto zájemce **počáteční zájmu** potenciálních zákazníků, kde můžeme sdílet informace o zákazníkovi, který vyjádřil zájmu o získání váš produkt. Zájemce je nejvyšší úrovni pořízení trychtýřového grafu.

    ![Získat nyní možnost](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

3.  Zákazník vybere "Koupit" v [webu Azure Portal](https://portal.azure.com/) získat váš produkt. Je tento vedoucí **active** potenciálních zákazníků, kde můžeme sdílet informace o zákazníkovi, který začal nasadit váš produkt.

    ![Kupte si možnost](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  Zákazník trvalo "Jednotka testu" pro vaši nabídku. Testovací verze jsou akcelerované příležitost, můžete okamžitě sdílet vaši firmu s potenciálními zákazníky bez jakékoli překážky k položce. Všechny testovací verze vygeneruje zájemce zákazníka, kterému se chcete o produktu Další informace. Další informace o Test jednotek při [testovací verze na AppSource](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Příklad testovací jednotky](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  Zákazník souhlasí s sdílení svých informací po výběru "Stáhnout". Je toto zájemce **počáteční zájmu** potenciálních zákazníků, kde můžeme sdílet informace o zákazníkovi, který vyjadřuje zájmu o získání váš produkt. Zájemce je nejvyšší úrovni pořízení trychtýřového grafu.

      ![Získat nyní možnost](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  Zákazník vybere "Obraťte se na mě" na vaši nabídku. Je tento vedoucí **aktivní** potenciálních zákazníků, kde můžeme sdílet informace o zákazníkovi, který požádá o následovat o produktu.

    ![Kontaktujte mě možnost](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Vedoucí dat
---------

Jednotlivé potenciální zákazníky, které obdržíte během procesu nákupu zákazníka má data v konkrétních polích. Vzhledem k tomu, že získáte potenciálních zákazníků z několika kroků, je nejlepší způsob, jak zpracovat zájemce zrušit duplicitní a přizpůsobit zpracování. Tímto způsobem každého zákazníka je stále odpovídající zprávu a vytváříte jedinečné spolupráci.

### <a name="lead-source"></a>Zdroje informací

Formát pro zdroj potenciálních zákazníků je **zdroj**-**akce** |  **nabídky**

**Zdroje**: "AzureMarketplace", "Generování auditních", "TestDrive" a "AppSource (SPZA)"

**Akce**:
- "In" – instalace. Tato akce je na webu Azure Marketplace nebo AppSource, když zákazník koupí produkt.
- "Čas načtení stránky"--zastupuje partnera vedla zkušební verze. Tato akce je na AppSource když zákazník používá kontaktu mě možnost.
- "DNC"--nekontaktujte. Tato akce je na AppSource, když se zkoušejí kontaktovat jako získá požadované partnerem, který byl křížové uvedené na stránce vaší aplikace. Sdílíme vedoucí nahoru, bylo daného zákazníka křížové uvedena ve vaší aplikaci, ale nemusí být kontaktován.
- Tato akce "Vytvořit"--je pouze uvnitř na webu Azure Portal a se vygeneruje, když zákazník koupí nabídky ke svému účtu.
- "StartTestDrive" – Tato akce je pouze pro testování jednotek a se vygeneruje, když zákazník spustí jejich testovací verze.

**Nabízí**

Následující příklady ukazují jedinečný Identifiers, které jsou přiřazeny k vydavatele a nabídky na konkrétní: checkpoint.check – bod r77-10sg-byol, bitnami.openedxcypress a docusign.3701c77e-1cfa - 4c 56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Informace o zákaznících

Pole v následujícím příkladu se zobrazí informace o zákazníkovi, který je obsažen v zájemce.
- Jméno: Jan
- Příjmení: Smith
- E-mailu: jsmith\@webu microsoft.com
- Phone: 1234567890
- Země: USA
- Společnost: Microsoft
- Title: technický ředitel

>[!Note]
>Všechna data v předchozím příkladu je vždy k dispozici pro jednotlivé potenciální zákazníky.

Aktivně pracujeme na zlepšení potenciálních zákazníků, takže pokud je datové pole, který nezobrazí tady ale by měl mít prosím [pošlete nám svůj názor](mailto:AzureMarketOnboard@microsoft.com).

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Jak se připojit váš systém CRM s portálem Cloud Partner
------------------------------------------------------------

Pokud chcete začít, získávání potenciálních zákazníků, jsme vytvořili naši vést Management connector na portál Cloud Partner tak, že můžete snadno připojit vaše informace CRM a budeme spojení za vás. Teď dokáže snadno využít zájemců vygenerovaný na webu marketplace bez významných technického úsilí pro integraci s externím systémem.

![Konektor může vést management](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Nám můžete napsat zájemce do různých systémů CRM nebo přímo do služby Azure Storage tabulky ve kterém můžete spravovat zájemce ale chcete. Každý z následujících odkazů poskytují pokyny pro připojení k realizace cíle:

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) pokyny o tom, jak nakonfigurovat pro získání zájemců Dynamics CRM Online.
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) se zobrazí pokyny pro nastavení konfigurace služby Marketo vést k získání potenciálních zákazníků.
-    [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md) se zobrazí pokyny pro nastavení vaše instance Salesforce pro získání potenciálních zákazníků.
-    [Azure Table](./cloud-partner-portal-lead-management-instructions-azure-table.md) se zobrazí pokyny pro nastavení vašeho účtu úložiště Azure pro získávání potenciálních zákazníků v Azure table.
-   [Koncový bod HTTPS](./cloud-partner-portal-lead-management-instructions-https.md) se zobrazí pokyny pro nastavení vašeho koncového bodu Https pro získání potenciálních zákazníků.

Po nakonfigurování cíl potenciálních zákazníků a publikování vaší nabídky, vytvoříme ověření připojení a odeslat testovací zájemce. Při zobrazení nabídky předtím, než můžete vysílat živě, můžete také otestovat připojení zájemce pokusu o získání nabídky v prostředí ve verzi preview. Je důležité, abyste měli jistotu, že vašeho pobytu nastavení zájemce aktuální tak, aby neztratili všechny potenciální zákazníky, proto se ujistěte, že je tato připojení aktualizovat pokaždé, když se něco změnilo na vaší straně.

<a name="what-next"></a>Co dál?
----------

Po technické nastavení je na místě, by měl obsahovat těchto potenciálních zákazníků na aktuální prodejní a marketingové strategie a provozních procesů. Máme hodně zajímá lépe pochopit celkový prodej zpracování a chcete úzce spolupracovat s vámi na poskytování vysoce kvalitní zájemce a dostatek dat, které vám zajistí úspěch. Vítáme váš názor na jsme způsob optimalizace a Vylepšete si potenciální zákazníci, který jsme vám poslali s dalšími daty, abyste se mohli úspěšně tito zákazníci. Dejte nám vědět, pokud máte zájem [poskytování zpětné vazby](mailto:AzureMarketOnboard@microsoft.com) a návrhy pro prodejní tým mohl být větších úspěchů s Marketplace vede umožní.
