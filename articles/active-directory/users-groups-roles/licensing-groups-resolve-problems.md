---
title: Řešení problémů přiřazení licencí pro skupinu - Azure Active Directory | Dokumentace Microsoftu
description: Tom, jak identifikovat a vyřešit problémy přiřazení licence, když používáte licencování na základě skupin Azure Active Directory
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.openlocfilehash: 2d12f07f8c65d3559f88de2c38c418ce4cc54188
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55510183"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identifikovat a vyřešit problémy přiřazení licencí pro skupinu v Azure Active Directory

Licencování pro skupiny ve službě Azure Active Directory (Azure AD) zavádí koncepci uživatelů v licencování chybovém stavu. V tomto článku vám vysvětlíme, důvody, proč může skončit uživatelé v tomto stavu.

Když přiřadíte licence přímo k jednotlivým uživatelům, bez použití skupinové licence, může selhat operace přiřazení. Například při spuštění rutiny prostředí PowerShell `Set-MsolUserLicense` v systému uživatele, rutina může selhat z mnoha důvodů, které se vztahují na obchodní logiku. Například může být dostatečný počet licencí nebo konflikt mezi dva plány služby, které nelze přiřadit ve stejnou dobu. Problém je okamžitě hlášení zpět.

Při použití skupinové licence, může dojít k chybám stejné, ale dějí na pozadí, zatímco služba Azure AD je přiřazení licencí. Z tohoto důvodu chyby nemůže být neprodleně vám. Místo toho jsou zaznamenány v objektu user a potom vykazovanou prostřednictvím portálu pro správu. Původní záměr licenci uživatele se nikdy ztraceny, ale je zaznamenán v chybovém stavu pro budoucí vyšetření a řešení.

## <a name="how-to-find-license-assignment-errors"></a>Jak najít chyby přiřazení licencí
**Najít chyby přiřazení licencí**

   1. K vyhledání uživatelů v chybovém stavu v konkrétní skupině, otevřete podokno pro skupinu. V části **licence**, zobrazí se upozornění, pokud jsou všechny uživatele v chybovém stavu.

   ![Skupina, oznamování chyb](./media/licensing-groups-resolve-problems/group-error-notification.png)

   2. Vyberte oznámení a otevřete seznam všech ovlivněných uživatelů. Můžete vybrat každého uživatele zvlášť, abyste zobrazili další podrobnosti.

   ![Skupiny, seznam uživatelů v chybovém stavu](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

   3. K vyhledání všech skupin, které obsahují alespoň jednu chybu na **Azure Active Directory** okně vyberte **licence**a pak vyberte **přehled**. Informační okno se zobrazí, když skupiny vyžadují vaši pozornost.

   ![Přehled, informace o skupinách v chybovém stavu](./media/licensing-groups-resolve-problems/group-errors-widget.png)

   4. Vyberte pole zobrazíte seznam všech skupin s chybami. Můžete vybrat každou skupinu další podrobnosti.

   ![Přehled, seznam skupin s chybami](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)


Následující části poskytují popis každý potenciální problém a způsob, jak problém vyřešit.

## <a name="not-enough-licenses"></a>Nedostatek licencí

**Problém:** Nejsou k dispozici dostatek dostupné licence pro některý z produktů, které je určené ve skupině. Budete muset zakoupit další licence k produktu nebo uvolnit nevyužitých licencí od jiných uživatelů nebo skupin.

Pokud chcete zobrazit, kolik licencí je k dispozici, přejděte na **Azure Active Directory** > **licence** > **všechny produkty**.

Pokud chcete zobrazit, kteří uživatelé a skupiny spotřebovávají licence, vyberte produkt. V části **licencovaní uživatelé**, zobrazí se seznam všech uživatelů, kteří měli licence přiřazené přímo nebo prostřednictvím jedné nebo více skupin. V části **licencované skupiny**, se zobrazí všechny skupiny, které mají tento produkty přiřazené.

**PowerShell:** Rutiny prostředí PowerShell, oznamte tuto chybu jako _CountViolation_.

## <a name="conflicting-service-plans"></a>Konfliktní plány služeb

**Problém:** Obsahuje některý z produktů, které je zadaná ve skupině pro plán služby této je v konfliktu s jinou plán služeb, který je již přiřazen uživateli prostřednictvím různých produktů. Některé nabízené plány služeb jsou nakonfigurovány tak, že nelze přiřadit jednomu uživateli jako jiné služby související plán.

Představte si následující příklad. Uživatel má licenci pro Office 365 Enterprise *E1* přímo, přiřazené všechny plány povolena. Uživatel přidal do skupiny, která má Office 365 Enterprise *E3* produktu přiřazené k němu. Produkt E3 obsahuje plány služby, které se nesmí překrývat s plány, které jsou součástí E1, tak skupiny přiřazení licence nezdaří s chybou "Konfliktní plány služeb". V tomto příkladu jsou konfliktní plány služeb:

-   SharePoint Online (plán 2) je v konfliktu se Sharepointem Online (plán 1).
-   Exchange Online (plán 2) je v konfliktu s Exchange Online (plán 1).

Pokud chcete tento konflikt vyřešit, je nutné zakázat, dva plány. Můžete zakázat E1 licence, která je přímo přiřazena uživateli. Nebo budete muset změnit přiřazení celou skupinu licencí a zakázat plány v licenci E3. Alternativně můžete rozhodnout odebrat licenci E1 od uživatele, pokud je redundantní v kontextu licence E3.

Rozhodnutí o tom, jak vyřešit konfliktní licence na produkty vždy patří správci. Azure AD nebude automaticky vyřešit konflikty licence.

**PowerShell:** Rutiny prostředí PowerShell, oznamte tuto chybu jako _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Na této licenci závisí další produkty.

**Problém:** Plán služby, který musí být povolené pro plán služby, v jiném produktu pro funkci obsahuje některý z produktů, které je zadaná ve skupině. K této chybě dochází, když se pokusí odebrat základní plán služby Azure AD. Například to může nastat při odebrání uživatele ze skupiny.

Chcete-li tento problém vyřešit, budete muset Ujistěte se, že vyžaduje plán se stále přiřadí uživatelům pomocí některé jiné metody nebo, že závislé služby je zakázáno pro tyto uživatele. Po to provedete, můžete z těchto uživatelů správně odebrat skupiny licencí.

**PowerShell:** Rutiny prostředí PowerShell, oznamte tuto chybu jako _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>Místo použití není povolené.

**Problém:** Některé služby nejsou k dispozici ve všech umístěních kvůli místní zákony a předpisy. Předtím, než můžete přiřadit licenci uživateli, je nutné zadat **místo využívání** vlastnost pro daného uživatele. Můžete zadat umístění v rámci **uživatele** > **profilu** > **nastavení** části webu Azure Portal.

Když Azure AD se pokusí přiřadit licenci skupiny na uživatele, jejichž využití umístění se nepodporuje, se nezdaří a zaznamená chybu na uživatele.

Chcete-li tento problém vyřešit, odeberte uživatele z nepodporované umístění z licencované skupiny. Případně pokud aktuální hodnoty umístění využití není představují umístění skutečné uživatele, můžete upravit je tak, aby licence jsou správně přiřazené příště (Pokud se podporuje na nové umístění).

**PowerShell:** Rutiny prostředí PowerShell, oznamte tuto chybu jako _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Pokud Azure AD přiřadí skupinu licencí, zdědí všechny uživatele bez použití zadaného umístění umístění adresáře. Doporučujeme správcům nastavit správné použití hodnoty umístění uživatelů před použitím skupinové licence pro dosažení souladu s místními zákony a předpisy.

## <a name="duplicate-proxy-addresses"></a>Duplicitní adresy proxy serveru

Pokud používáte Exchange Online, někteří uživatelé ve vašem tenantovi může být nesprávně nakonfigurování se stejnou hodnotou adresy proxy. Když licencování na základě skupiny se pokusí přiřadit licenci na tyto uživatele, se nezdaří a zobrazí "adresa proxy serveru se už používá".

> [!TIP]
> Pokud chcete zobrazit, pokud je adresa duplicitní proxy, spusťte následující rutinu prostředí PowerShell pro Exchange Online:
```
Run Get-Recipient | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
```
> Další informace o tomto problému najdete v tématu ["adresa proxy serveru se už používá" chybová zpráva v Exchangi Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). Tento článek obsahuje také informace o [jak se připojit ke službě Exchange Online pomocí vzdáleného prostředí PowerShell](https://technet.microsoft.com/library/jj984289.aspx). Tento článek pro další informace naleznete v tématu [na způsob naplnění atributu proxyAddresses v Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

Až vyřešíte všechny problémy adresu proxy serveru pro ovlivnění uživatelé, ujistěte se, že chcete vynutit zpracování licencí skupiny, abyste měli jistotu, že licence se teď může používat.

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>Co se stane, když je ve skupině více než jednu licenci na produkt?

Pro skupinu můžete přiřadit více než jednu licenci na produkt. Například můžete přiřadit Office 365 Enterprise E3 a Enterprise Mobility + Security skupinu a snadno povolit všechny služeb pro uživatele.

Azure AD se pokusí přiřadit všechny licence, které jsou zadány ve skupině k jednotlivým uživatelům. Pokud Azure AD nejde přiřadit některý z produktů z důvodu problémů obchodní logiky, nebude by buď přidělit jiným licencím ve skupině. Příkladem je, pokud nejsou k dispozici dostatek licencí pro všechny, nebo pokud je v konfliktu s ostatními službami, které jsou povolené na uživatele.

Zobrazí se uživatelům, kteří se nepodařilo přiřadit a zkontrolovat, které produkty jsou tento problém ovlivňuje.

## <a name="what-happens-when-a-group-with-licenses-assigned-is-deleted"></a>Co se stane, když skupinu přiřazené licence je odstranit?

Musíte odebrat všechny licence přiřazené ke skupině před odstraněním skupiny. Odebrání licencí od všech uživatelů ve skupině může trvat dobu. Při odebírání přiřazení licencí ze skupiny, může být selhání, pokud má uživatel přiřazenou licenci závislé nebo pokud je problém konflikt adres proxy serveru, který zakazuje odebrání licencí. Pokud má uživatel licenci, která závisí na licenci, která se odebírá z důvodu odstranění skupiny, přiřazení licence uživateli je převést z zděděné pro přesměrování.

Představte si třeba skupinu, která má Office 365 E3 nebo E5 přiřazena pomocí Skypu pro firmy plán služby povolena. Představte si také, že několik členů skupiny mají přímo přiřazené licence zvuk konference. Při odstranění skupiny licencování na základě skupiny pokusí se odebrat všechny uživatele Office 365 E3 nebo E5. Protože konference zvuk je závislá na Skype pro firmy, pro všechny uživatele s zvuk konference přiřazené, na základě skupin licencí převede licence Office 365 E3 nebo E5 na přímé přiřazení licencí.

## <a name="how-do-you-manage-licenses-for-products-with-prerequisites"></a>Jak budete spravovat licence pro produkty s požadavky?

Některé produkty Microsoft Online může vlastnit jsou *doplňky*. Doplňky vyžadují plán požadovaných součástí služby povolit předtím, než je možné přiřadit licence pro uživatele nebo skupinu. S licencováním na základě skupin, bude systém vyžadovat, že požadavku a doplněk plánech nacházet ve stejné skupině. To slouží k zajištění, že všichni uživatelé, kteří jsou přidány do skupiny, dostanou plně funkční produkt. Pojďme se podívat v následujícím příkladu:

Microsoft Workplace Analytics je doplňkovým produktem. Obsahuje plán jednu službu se stejným názvem. Tento plán služby jsme pouze můžete přiřadit uživateli nebo skupině, pokud jeden z následujících požadovaných součástí je také přiřazený:
- Exchange Online (plán 1) 
- Exchange Online (plán 2)

Snažíme se přiřazení sama o sobě tohoto produktu do skupiny, portál se vrátí chybu. Výběr oznamování chyb se zobrazí následující podrobnosti:

![Skupina, požadovaných součástí chybí](./media/licensing-groups-resolve-problems/group-prerequisite-required.png)

Pokud vybereme možnost podrobnosti, zobrazí se následující chybová zpráva:

>Operace licence neproběhla úspěšně. Ujistěte se, že skupina má potřebné služby, než přidáte nebo odeberete závislou službou. **Služba Microsoft Workplace Analytics vyžaduje, aby Exchange Online (plán 2) Chcete-li se povolila i.**

K přiřazení této licence na doplněk do skupiny, musí zajišťujeme, tato skupina obsahuje také plánu požadované služby. Například můžeme aktualizovat existující skupinu, která už obsahuje plné verze produktu Office 365 E3 a potom k němu přidejte doplňkovým produktem.

Je také možné vytvořit samostatné skupiny, které obsahuje pouze minimální požadované produkty aby doplněk pracovat. Je možné získat licenci jenom vybraným uživatelům doplňkovým produktem. V tomto příkladu jsme do stejné skupiny přiřadili následující produkty:
- Office 365 Enterprise E3 s Exchange Online (plán 2) plánu služby povolena
- Microsoft Workplace Analytics

![Zahrnuté skupiny, požadovaných součástí](./media/licensing-groups-resolve-problems/group-addon-with-prerequisite.png)

Od této chvíle přidané do této skupiny uživatele používat jednu licenci na produkt E3 a jednu licenci na produkt Workplace Analytics. Ve stejnou dobu tito uživatelé můžou být členy jiné skupiny, která jim uděluje plný produkt E3 a jsou i nadále spotřebovávají pouze jedna licence pro tento produkt.

> [!TIP]
> Můžete vytvořit více skupin pro jednotlivé požadované služby. Například pokud používáte Office 365 Enterprise E1 a Office 365 Enterprise E3 pro vaše uživatele, můžete vytvořit dvě skupiny, abyste získali licenci pro Microsoft Workplace Analytics: ten, který používá E1 jako předpoklad a druhý, který se používá E3. Tímto způsobem můžete distribuovat doplněk E1 a E3 uživatelům nutnosti využít další licence.

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>Jak můžete vynutit zpracování licencí skupiny pro vyřešení chyb?

V závislosti na tom, jaké kroky jste provést k vyřešení chyby může být potřeba ručně aktivujete zpracování skupiny k aktualizaci stavu uživatele.

Například pokud některé licence uvolnit tak, že odeberete přiřazení přímých licencí od uživatelů, budete muset aktivovat zpracování skupiny, které se dříve nepodařilo plně všechny členy uživatelské licence. Za účelem opětovného zpracování skupiny, přejděte do podokna skupinu, otevřete **licence**a pak vyberte **znovu zpracovat** tlačítko na panelu nástrojů.

## <a name="how-do-you-force-license-processing-on-a-user-to-resolve-errors"></a>Jak můžete vynutit zpracování licence pro uživatele pro vyřešení chyb?

V závislosti na tom, jaké kroky jste provést k vyřešení chyby může být potřeba ručně aktivujete zpracování uživateli aktualizovat stav uživatele.

Například po vyřešení problému duplicitní proxy adres pro ovlivněného uživatele, budete muset aktivovat zpracování uživatele. Za účelem opětovného zpracování uživatele, přejděte do podokna uživatele, otevřete **licence**a pak vyberte **znovu zpracovat** tlačítko na panelu nástrojů.

## <a name="next-steps"></a>Další postup

Další informace o další scénáře pro správu licencí pomocí skupin, naleznete v následujících tématech:

* [Co je skupina založená na licencování v Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Přiřazení licencí ke skupině v Azure Active Directory](licensing-groups-assign.md)
* [Postup migrace jednotlivě licencovaných uživatelů na licencování na základě skupin v Azure Active Directory](licensing-groups-migrate-users.md)
* [Migrace uživatelů mezi licencemi produktů pomocí licencování pro skupiny ve službě Azure Active Directory](licensing-groups-change-licenses.md)
* [Další scénáře licencování na základě skupin v Azure Active Directory](licensing-group-advanced.md)
* [Příklady prostředí PowerShell pro licencování na základě skupiny v Azure Active Directory](licensing-ps-examples.md)
