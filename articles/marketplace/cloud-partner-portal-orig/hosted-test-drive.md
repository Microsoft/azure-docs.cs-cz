---
title: Hostovaná testovací verze | Dokumentace Microsoftu
description: Jak nastavit udržování na Marketplace hostovaná testovací verze
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 1ab9fcd50ad7081f8047d62e545287fa75db75e4
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809658"
---
# <a name="hosted-test-drive"></a>Hostovaná testovací verze

Hostovaná testovací verze složitost instalační program odebere služba Microsoft hosting a zachovat službu, která provádí Test Drive uživatele zřizování a zrušení zřizování. Tento článek je určený pro vydavatele, kteří mají jejich nabídek zveřejněných na AppSource nebo vytváříte nové a chcete nabízet hostovaná testovací verze, které přístup k Dynamics 365 for Customer Engagement, Dynamics 365 pro Finance a operace nebo Dynamics 365 Business Central instance.

## <a name="how-to-publish-a-test-drive"></a>Jak publikovat testovací verze

Přejděte do existující nabídky nebo vytvořte novou nabídku.

Vyberte možnost vyzkoušet testovací jízdu v nabídce na straně.

Vyberte \'Ano\' pro \'povolit si testovací jízdu\' možnost.

Zadejte následující pole v \'podrobnosti\' oddílu.

- **Popis**: poskytovat přehled o vaše testovací verze. Tento text se zobrazí uživateli, zatímco testovací verze se zřizuje. Toto pole podporuje HTML, pokud byste chtěli poskytnout formátovaný obsah.
- **Uživatelská příručka**: nahrání podrobné uživatelská příručka (soubor typu .pdf), což pomáhá pochopit, jak použít aplikaci pro uživatele testovací verze.
- **Test Drive ukázkové Video**: Volitelně můžete nahrát video, které představuje vaši aplikaci.

AppSource udělení oprávnění k zřídit a zrušit jejich zřízení Test Drive uživatelé ve vašem tenantovi, postupujte podle pokynů nachází [tady](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

V tomto kroku vygenerujete \'Id aplikace Azure AD\' a \'klíče aplikace služby Azure AD\' hodnot uvedených níže.

Zadejte následující pole v \'technické konfigurace\' části:

- **Typ testu jednotky**: Zvolte \'Hosted Microsoft (příklad Dynamics 365 for Customer Engagement)' možnost. To znamená, že Microsoft bude hostovat a spravovat služby, který provádí Test Drive uživatele zřizování a zrušení zřizování.
- **Maximální počet souběžných testovacími verzemi**: nastavte pole na počet souběžných uživatelů, které může mít aktivní testovací verze v libovolném časovém okamžiku dobu. Každý uživatel bude využívat licence Dynamics, jejich Test Drive je aktivní, proto budete muset zajistit, že máte alespoň tolik Dynamics licencí k dispozici pro uživatele testovací verze. Doporučená hodnota 3 – 5.
- **Jednotka doba trvání testu (hodiny)**: nastavte pole na počet hodin, uživatelé budou aktivní pro testovací verze. Po tomto počtu hodin bude se uživatel zrušit zřízení z vašeho tenanta. Doporučená hodnota 2 až 24 hodin v závislosti na složitosti vaší aplikace. Je-li spustit mimo časový limit a chtějí mít přístup k testovací verze, může uživatel vždy požádat jiného testovací verze.
- **Adresa URL instance**: Zadejte adresu URL, která uživatel testovací verze se zpočátku přejde při jejich spuštění na testovací verzi. Toto je obvykle adresa URL instance Dynamics 365, který má vaše aplikace a ukázková data nainstalována. Příklad hodnoty:https://testdrive.crm.dynamics.com
- **ID klienta Azure AD**: Zadejte ID Tenanta Azure pro vaši instanci Dynamics 365. Načíst tuto hodnotu, přihlaste se k webu Azure portal a přejděte do \'Azure Active Directory\'  - \> vybrat vlastnosti v okně nabídky -\> zkopírujte ID adresáře. Příklad hodnoty: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **ID aplikace Azure AD**: ID aplikace Azure AD, kterou jste vytvořili v kroku 7. \ Příklad hodnoty: 53852862-a2ae-4e43-9461-faa49650a096
- **Klíč aplikace Azure AD**: tajný klíč pro službu Azure AD App vytvořili v kroku 7. \ Příklad hodnoty: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk =
- **Název Tenanta Azure AD**: Zadejte název Tenanta Azure pro vaši instanci Dynamics 365. Použijte formát \<tenantname.\> onmicrosoft.com. Příklad hodnoty: testdrive.onmicrosoft.com
- **Instance adresy URL webového rozhraní API**: Zadejte adresu URL webového rozhraní API pro vaši instanci Dynamics 365. Tuto hodnotu můžete načíst tak, že přihlášení k vaší instanci Microsoft Dynamics 365 a přejdete do nastavení -\> přizpůsobení -\> materiály pro vývojáře –\> Instance webové rozhraní API (zkopírujte tuto adresu URL). Příklad hodnoty:  https://testdrive.crm.dynamics.com/api/data/v9.0 
- **Název role**: Zadejte název vlastní roli zabezpečení Dynamics 365, kde je jste vytvořili pro testovací verze. Toto je role, která se přiřadí uživatele co nejhladší jejich testovací verze. Příklad hodnoty: testdriverole

## <a name="next-steps"></a>Další postup

Jakmile budete připraveni **publikovat** vaší nabídky, po uplynutí aplikace certifikaci, budete mít **ve verzi preview** o vaší nabídce. Spusťte si testovací jízdu v uživatelském rozhraní a zkontrolujte, že vaše testovací verze běží správně. Jakmile budete s klidem s vaší nabídkou ve verzi preview, nyní je čas **vysílat živě!**
