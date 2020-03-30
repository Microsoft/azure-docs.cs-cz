---
title: Hostovaná testovací jízda | Azure Marketplace
description: Jak nastavit udržovat webovou webovou testovací jednotku hostovací
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d054064e236e121e02bf58a0eb73b5a62f24a09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278259"
---
# <a name="hosted-test-drive"></a>Hostovaná testovací verze

Hostovaná testovací jednotka odstraňuje složitost instalace podle hostování společnosti Microsoft a udržuje službu, která provádí zřizování a odstraňování zřízení uživatele testovací jednotky. Tento článek je určen pro vydavatele, kteří mají svou nabídku na AppSource nebo budují novou a chtějí nabídnout hostovku testovací disku, která se spojuje s Dynamics 365 pro customer engagement, Dynamics 365 for Finance and Operations nebo Dynamics 365 Business Central Instance.

## <a name="how-to-publish-a-test-drive"></a>Jak publikovat testovací jízdu

Přejděte na existující nabídku nebo vytvořte novou nabídku.

Z boční nabídky vyberte možnost Testovací jednotka.

\'Vyberte\' \'Ano pro\' možnost Povolit testovací jednotku.

V části Podrobnosti \'\' uveďte následující pole.

- **Popis**: Poskytněte přehled o testovací jízdě. Tento text se zobrazí uživateli při zřizování testovací jednotky. Toto pole podporuje html, pokud chcete poskytnout formátovaný obsah.
- **Uživatelská příručka**: Nahrajte podrobnou uživatelskou příručku (soubor typu PDF), která uživatelům testovacího disku pomůže pochopit, jak používat vaši aplikaci.
- **Ukázkové video testovací hodu**: Volitelně nahrajte video, které představuje vaši aplikaci.

Udělte oprávnění AppSource k zřízení a zrušení zřízení uživatelů testovací jednotky ve vašem tenantovi pomocí pokynů [umístěných zde](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

V \'tomto kroku vygenerujete ID\' aplikace \'Azure AD\' a Azure AD Hodnoty klíče aplikace uvedené níže.

V části \'Technická konfigurace\' uveďte následující pole:

- **Typ testovací jednotky** \': Zvolte možnost Hostované houfnice (například Dynamics 365 for Customer Engagement). To znamená, že společnost Microsoft bude hostitelem a udržovat službu, která provádí zřizování a zrušení zřízení uživatele testovací jednotky.
- **Maximální počet souběžných testovacích jednotek**: Toto pole nastavte na počet souběžných uživatelů, kteří mohou mít aktivní testovací jednotku v daném okamžiku. Každý uživatel bude využívat licenci Dynamics, když je jejich testovací jednotka aktivní, takže budete muset zajistit, že máte k dispozici alespoň tolik licencí Dynamics pro uživatele testovací jednotky. Doporučená hodnota 3-5.
- **Doba trvání testovací jednotky (hodiny):** Nastavte toto pole na počet hodin, pro které budou uživatelé testovat. Po uplynutí této doby bude uživatel zbaven zřízení z vašeho tenanta. Doporučená hodnota 2-24 hodin v závislosti na složitosti vaší aplikace. Uživatel může vždy požádat o další testovací disku, pokud jim dojde čas a chcete znovu získat přístup k testovací jízdě.
- **Adresa URL instance**: Zadejte adresu URL, na kterou bude uživatel testovací jednotky nejprve navigován při spuštění testovací jednotky. Obvykle se jedná o adresu URL instance Dynamics 365, na kterou je nainstalována vaše aplikace a ukázková data. Příklad hodnoty:\/https: /testdrive.crm.dynamics.com
- **ID klienta Azure AD**: Zadejte ID klienta Azure pro vaši instanci Dynamics 365. Chcete-li tuto hodnotu načíst, \'přihlaste se na portál Azure Portal a přejděte na Azure Active Directory\'  - \> Select Properties z okna nabídky –\> Zkopírujte ID adresáře. Příklad hodnoty: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **ID aplikace Azure AD:** ID aplikace Azure AD, kterou jste vytvořili v kroku 7.\ Příklad hodnota: 53852862-a2ae-4e43-9461-faa49650a096
- **Klíč aplikace Azure AD:** Tajný klíč pro aplikaci Azure AD vytvořenou v kroku 7.\ Příklad hodnota: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Název klienta Azure AD**: Zadejte název tenanta Azure pro vaši instanci Dynamics 365. Použijte formát \<název_tenanta. \>onmicrosoft.com. Příklad hodnoty: testdrive.onmicrosoft.com
- **Adresa URL webového rozhraní API instance:** Zadejte adresu URL webového rozhraní API pro instanci Dynamics 365. Tuto hodnotu můžete načíst tak, že se přihlásíte do\> instance\> aplikace\> Microsoft Dynamics 365 a přejdete na nastavení – přizpůsobení – prostředky pro vývojáře – rozhraní API instance (Zkopírujte tuto adresu URL). Příklad hodnoty:\/https: /testdrive.crm.dynamics.com/api/data/v9.0
- **Název role**: Zadejte název vlastní role zabezpečení Dynamics 365, kterou jste vytvořili pro testovací jednotku. Toto je role, která bude přiřazena uživatelům během jejich testovací pokus. Příklad hodnoty: testdriverole

## <a name="next-steps"></a>Další kroky

Až budete připraveni **zveřejnit** svou nabídku, až vaše aplikace projde certifikací, budete mít **náhled** nabídky. Spusťte testovací jednotku v ui a ověřte, zda testovací jednotky běží správně. Jakmile se budete cítit pohodlně s vaší nabídkou náhledu, nyní je čas **jít živě!**
