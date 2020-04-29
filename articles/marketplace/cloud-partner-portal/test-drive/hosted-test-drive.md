---
title: Hostovaný testovací disk | Azure Marketplace
description: Jak nastavit údržbu hostovaného testovacího disku pro Marketplace
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d054064e236e121e02bf58a0eb73b5a62f24a09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278259"
---
# <a name="hosted-test-drive"></a>Hostovaná testovací verze

Hostovaná testovací jednotka odstraní složitost nastavení hostováním Microsoftu a udržuje službu, která provádí testování a zřizování uživatelů pomocí testovacích jednotek. Tento článek je určen vydavatelům, kteří mají svou nabídku na AppSource nebo vytváří novou a chtějí nabízet hostovaný testovací disk, který se připojuje k Dynamics 365 pro zákaznickou zapojení, Dynamics 365 pro finance a operace nebo Dynamics 365 Business Central.

## <a name="how-to-publish-a-test-drive"></a>Postup publikování testovacího disku

Přejděte na existující nabídku nebo vytvořte novou nabídku.

V postranní nabídce vyberte možnost testovací jednotka.

Pro \' \'možnost\' povolit testovací jednotku\' vyberte Ano.

V části \'podrobnosti\' zadejte následující pole.

- **Popis**: zadejte přehled testovacích jednotek. Tento text se zobrazí uživateli při zřizování testovací jednotky. Toto pole podporuje kód HTML, pokud chcete zadat formátovaný obsah.
- **Příručka uživatele**: nahrání podrobného uživatelského ručního (soubor typu. PDF), který pomáhá testovat, jak používat vaši aplikaci.
- **Ukázkové video ke zkušební jednotce**: Volitelně můžete nahrát video, které prezentuje vaši aplikaci.

Udělte AppSource oprávnění ke zřízení a zrušení zřízení uživatelů testovacích jednotek ve vašem tenantovi pomocí pokynů, které najdete [tady](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

V \'tomto kroku vygenerujete aplikace Azure AD Id\' a \'aplikace Azure AD hodnoty klíče\' uvedené níže.

V části \'technická konfigurace\' zadejte následující pole:

- **Typ testovací jednotky**: vyberte \'možnost Microsoft Hosted (příklad Dynamics 365 for Customer Engagement). To znamená, že Microsoft bude hostovat a udržovat službu, která provede test zřizování a zrušení zřizování uživatelů.
- **Maximální počet souběžných testovacích jednotek**: Toto pole nastavte na počet souběžných uživatelů, kteří můžou mít v jakémkoli okamžiku aktivní testovací jednotku. Každý uživatel bude používat licenci Dynamics v době, kdy je jejich testovací jednotka aktivní, takže budete muset zajistit, abyste měli k dispozici aspoň tento počet licencí Dynamics pro uživatele testovacích jednotek. Doporučená hodnota 3-5.
- **Doba trvání testovacích jednotek (hodiny)**: nastavte toto pole na počet hodin, po které budou uživatelé testovacích jednotek aktivní. Po uplynutí této doby bude uživatel ve vašem tenantovi odřízen. Doporučená hodnota 2-24 hodin v závislosti na složitosti aplikace Uživatel si může vždycky vyžádat jinou testovací jednotku, pokud k nim dojde nevčas a chcete znovu získat přístup k testovací jednotce.
- **Adresa URL instance**: zadejte adresu URL, na kterou bude uživatel testovací jednotky zpočátku přesměrován při spuštění testovací jednotky. Obvykle se jedná o adresu URL vaší instance Dynamics 365, na které je nainstalovaná vaše aplikace a ukázková data. Příklad hodnoty: https:\//testdrive.CRM.Dynamics.com
- **ID tenanta Azure AD**: Zadejte ID tenanta Azure pro vaši instanci Dynamics 365. Pokud chcete tuto hodnotu načíst, přihlaste se Azure Portal \'a\'  - \> přejděte na Azure Active Directory vybrat vlastnosti z\> okna nabídky – zkopírovat ID adresáře. Ukázková hodnota: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **Aplikace Azure AD ID**: ID aplikace Azure AD vytvořené v kroku 7. \ příklad hodnoty: 53852862-a2ae-4e43-9461-faa49650a096
- **Aplikace Azure AD Key**: tajný klíč pro aplikace Azure AD vytvořený v kroku 7. \ příklad hodnoty: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk =
- **Název tenanta Azure AD**: zadejte název tenanta Azure pro vaši instanci Dynamics 365. Použijte formát \<tenanta. \>onmicrosoft.com. Ukázková hodnota: testdrive.onmicrosoft.com
- **Instance URL webového rozhraní API**: zadejte adresu URL webového rozhraní API pro vaši instanci Dynamics 365. Tuto hodnotu můžete načíst tak, že se přihlásíte do vaší instance Microsoft Dynamics 365 a přejdete na nastavení-\> přizpůsobení-\> Developer sources –\> instance Web API (zkopírujte tuto adresu URL). Příklad hodnoty: https:\//testdrive.CRM.Dynamics.com/API/data/v9.0
- **Název role**: zadejte název vlastní role zabezpečení Dynamics 365, kterou jste vytvořili pro testovací jednotku. Toto je role, která se uživatelům přiřadí během své testovací jednotky. Ukázková hodnota: testdriverole

## <a name="next-steps"></a>Další kroky

Po dokončení **publikování** vaší nabídky si po úspěšném dokončení vaší aplikace budete mít **Náhled** své nabídky. Spusťte testovací jednotku v uživatelském rozhraní a ověřte, zda jsou správně spuštěny testovací jednotky. Jakmile budete mít v nabídce Preview pohodlí, teď je to čas do **Live!**
