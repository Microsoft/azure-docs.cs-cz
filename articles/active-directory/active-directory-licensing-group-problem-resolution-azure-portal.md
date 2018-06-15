---
title: Vyřešit problémy s licencí pro skupinu v Azure Active Directory | Microsoft Docs
description: Jak identifikovat a vyřešit problémy přiřazení licencí, pokud používáte Azure Active Directory na základě skupiny licencí
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e91b0a79e7b45ce7e0de1b7cf4aa3123550692af
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763712"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identifikovat a vyřešit problémy přiřazení licence pro skupinu v Azure Active Directory

Na základě skupiny licencování v Azure Active Directory (Azure AD) zavádí koncepci uživatele v licencování chybový stav. V tomto článku vám vysvětlíme, z důvodů, proč se uživatelé muset zvýšit v tomto stavu.

Po přiřazení licence přímo pro jednotlivé uživatele, bez použití správy na základě skupiny licencí, může selhat operace přiřazení. Například při spuštění rutiny prostředí PowerShell `Set-MsolUserLicense` na uživatele systému, rutina selhat z mnoha důvodů, které se vztahují na obchodní logiku. Například může být dostatečný počet licencí nebo konfliktu mezi dvěma plány služby, které nelze přiřadit ve stejnou dobu. Problém je okamžitě hlášeny na vás.

Při použití na základě skupiny licencí, může dojít k chybám stejné, ale dojít na pozadí při služby Azure AD je přiřazování licencí. Z tohoto důvodu chyby nelze oznamovat okamžitě. Místo toho se zaznamenávají v objektu user a pak hlášené prostřednictvím portálu pro správu. Původní záměr licence uživatele se nikdy ztraceny, ale se zaznamenává v chybovém stavu pro budoucí šetření a překlad.

## <a name="how-to-find-license-assignment-errors"></a>Postup nalezení chyb přiřazení licencí
**K vyhledání chyby přiřazení licencí**

   1. K vyhledání uživatele v chybovém stavu ve specifické skupině, otevřete podokno pro skupinu. V části **licence**, zobrazí se upozornění, pokud jsou všechny uživatele v chybovém stavu.

   ![Skupiny, oznámení o chybě](media/active-directory-licensing-group-problem-resolution-azure-portal/group-error-notification.png)

   2. Vyberte oznámení, které otevřete seznam všech ovlivněných uživatelů. Můžete vybrat každého uživatele jednotlivě, abyste zobrazili další podrobnosti.

   ![Skupiny, seznam uživatelů v chybovém stavu](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-users-with-errors.png)

   3. K vyhledání všech skupin, které obsahují alespoň jednu chybu na **Azure Active Directory** okně vyberte **licence**a potom vyberte **přehled**. Pokud skupiny vyžadují vaši pozornost, zobrazí se okno s informacemi.

   ![Přehled, informace o skupinách v chybovém stavu](media/active-directory-licensing-group-problem-resolution-azure-portal/group-errors-widget.png)

   4. Zaškrtněte políčko Zobrazit seznam všech skupin s chybami. Můžete vybrat každou skupinu další podrobnosti.

   ![Přehled, seznam skupin s chybami](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-groups-with-errors.png)


V následujících oddílech popis každý potenciální problém a způsobu jeho řešení.

## <a name="not-enough-licenses"></a>Nedostatek licencí

**Problém:** nejsou k dispozici dostatek dostupné licence pro jeden z produktů, které je zadána ve skupině. Budete muset zakoupit další licence pro produkt nebo uvolněte nevyužitých licencí z jiné uživatele nebo skupiny.

Pokud chcete zobrazit, kolik licencí je k dispozici, přejděte na **Azure Active Directory** > **licence** > **všechny produkty**.

Pokud chcete zobrazit, kteří uživatelé a skupiny spotřebovávají licencí, vyberte produkt. V části **licencované uživatele**, zobrazí se seznam všech uživatelů, kterým byl přiřazen přímo nebo prostřednictvím jedné nebo více skupin licencí. V části **licenci skupiny**, zobrazení všech skupin, které mají tento produkty přiřazené.

**Prostředí PowerShell:** rutiny prostředí PowerShell, ohlaste tuto chybu jako _CountViolation_.

## <a name="conflicting-service-plans"></a>Konfliktní plány služeb

**Problém:** jeden z produktů, které je zadána ve skupině obsahuje plán služeb, který je v konfliktu s jinou plán služeb, který je již přiřazen k uživatele na základě různých produktů. Některé plány služby jsou nakonfigurovány tak, že nemůže být přiřazen stejného uživatele, který plán jiný, související služby.

Podívejte se na následující příklad. Uživatel, který má licenci pro Office 365 Enterprise *E1* přímo, má přiřazeny všechny plány povolena. Uživatel přidal do skupiny, která má Office 365 Enterprise *E3* produktu přiřazen. Produkt E3 obsahuje plány služby, které se nesmí překrývat s plány, které jsou součástí E1, takže přiřazení licence skupiny selže s chybou "Konfliktní služby plány". V tomto příkladu jsou konfliktní plány služeb:

-   SharePoint Online (plán 2) v konfliktu s SharePoint Online (plán 1).
-   Exchange Online (plán 2) je v konfliktu s Exchange Online (plán 1).

Chcete-li vyřešit tento konflikt, je nutné zakázat dva plánů. Můžete zakázat E1 licenci, která je přímo přiřazena uživateli. Nebo, je potřeba změnit přiřazení licence celé skupiny a zakázat plány v licenci E3. Alternativně můžete rozhodnout odebrat licence E1 od uživatele, pokud je redundantní v rámci licence E3.

Rozhodnutí o tom, jak vyřešit konfliktní licence k produktům vždy patří správci. Azure AD automaticky nepřekládá konfliktu licencí.

**Prostředí PowerShell:** rutiny prostředí PowerShell, ohlaste tuto chybu jako _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Na této licenci závisí další produkty.

**Problém:** jeden z produktů, které je zadána ve skupině obsahuje plán služeb, který musí být povolen pro jiný plán služby, v jiném produktu, funkce. K této chybě dojde, když se pokusí odebrat základní plán služby Azure AD. Například to může dojít v případě odeberte uživatele ze skupiny.

Chcete-li tento problém vyřešit, zkontrolujte, že vyžaduje plán je stále přiřadit uživatelům přes nějaké jiné metody nebo že závislých služeb pro tyto uživatele vypnuté. Po učinit, můžete od těchto uživatelů odebrat správně skupiny licencí.

**Prostředí PowerShell:** rutiny prostředí PowerShell, ohlaste tuto chybu jako _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>Místo využívání není povolen.

**Problém:** některé služby společnosti Microsoft nejsou k dispozici ve všech umístěních z důvodu místních zákonů a nařízení. Než bude možné přiřadit licence pro uživatele, je nutné zadat **umístění využití** vlastnost pro uživatele. Můžete zadat umístění pod **uživatele** > **profil** > **nastavení** části portálu Azure.

Pokud Azure AD došlo k pokusu o přiřazení skupiny licencí pro uživatele, jejichž umístění využití není podporována, se nezdaří a zaznamenává chybu na uživatele.

Chcete-li tento problém vyřešit, odeberte uživatele z nepodporované umístění z licencované skupiny. Případně pokud aktuální umístění hodnoty využití nemáte představují umístění skutečné uživatele, můžete upravit je tak, aby licence jsou správně přiřadit dalším spuštění (Pokud je podporována nové umístění).

**Prostředí PowerShell:** rutiny prostředí PowerShell, ohlaste tuto chybu jako _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Pokud Azure AD přiřadí skupinu licencí, zdědí všechny uživatele bez použití zadané umístění umístění adresáře. Doporučujeme vám, že správci nastavit správné použití hodnoty umístění na uživatelé před použitím na základě skupiny licencí pro dosažení souladu s místními zákony a nařízení.

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>Co se stane, když je více než jednu licenci produktu na skupinu?

Více než jednu licenci produktu můžete přiřadit ke skupině. Například můžete přiřadit Office 365 Enterprise E3 a Enterprise Mobility + Security do snadno povolit všechny obsažených služeb pro uživatele skupiny.

Azure AD došlo k pokusu o přiřazení všechny licence, které jsou určené ve skupině pro každého uživatele. Pokud Azure AD nelze přiřadit jeden z produktů z důvodu obchodní logiky problémy, se nebude buď přiřadit licence ve skupině. Příkladem je, pokud nejsou k dispozici dostatek licencí pro všechny, nebo pokud dochází ke konfliktům s jinými službami, které jsou povoleny na uživatele.

Můžete zobrazit uživatele, kteří přiřazovány a zkontrolujte tento problém se týká produktů, které se nezdařilo.

## <a name="how-do-you-manage-licenses-for-products-with-prerequisites"></a>Jak budete spravovat licence pro produkty s požadavky?

Jsou některé z těchto produktů Microsoft Online může vlastní *doplňky*. Rozšíření vyžadují plán požadovaných služeb potřeba povolit pro uživatele nebo skupinu, dříve než může být přiřazeny licenci. S na základě skupiny licencí, systém vyžaduje, aby plány služby požadované a rozšíření součástí stejné skupiny. Důvodem je, že bude potřeba zajistit, že všechny uživatele, kteří jsou přidáni do skupiny můžete produkt plně funkční. Zvažte následující příklad:

Microsoft Analytics síti na pracovišti je doplňkovým produktem. Obsahuje plán jedné služby se stejným názvem. Tento plán služby jsme pouze můžete přiřadit na uživatele nebo skupinu, pokud jeden z těchto předpokladů je také přiřazený:
- Exchange Online (plán 1) 
- Exchange Online (plán 2)

Pokud se pokusíme se přiřazení tohoto produktu na svůj vlastní do skupiny, na portálu vrátí chybu. Výběr oznámení o chybě obsahuje následující podrobnosti:

![Skupiny, požadovaných součástí chybí](media/active-directory-licensing-group-problem-resolution-azure-portal/group-prerequisite-required.png)

Pokud jsme vyberte podrobnosti, zobrazí se následující chybová zpráva:

>Licence operace se nezdařila. Ujistěte se, zda má skupina nezbytné služby před přidáním nebo odebráním závislá služba. **Služba Microsoft Analytics síti na pracovišti vyžaduje Exchange Online (plán 2) a povolení.**

Pokud chcete přiřadit tuto licenci rozšíření do skupiny, jsme potřeba zkontrolovat, tato skupina obsahuje taky plán požadovaných služeb. Například můžeme aktualizovat existující skupinu, která již obsahuje plnou verzi produktu Office 365 E3 a potom k němu přidejte doplňkovým produktem.

Je také možné vytvořit samostatnou skupinu, která obsahuje jenom minimální požadované produkty aby doplňku fungovat. Může sloužit k licence jenom Vybraní uživatelé produktu rozšíření. V tomto příkladu jsme do stejné skupiny přiřadili následující produkty:
- Office 365 Enterprise E3 s Exchange Online (plán 2) plán služeb povoleno
- Analýza Microsoft síti na pracovišti

![Zahrnout skupiny, požadovaných součástí.](media/active-directory-licensing-group-problem-resolution-azure-portal/group-addon-with-prerequisite.png)

Od této chvíle přidané do této skupiny uživatele používat jednu licenci produktu E3 a jednu licenci produktu Analytics síti na pracovišti. Ve stejnou dobu tito uživatelé mohou být členy jiné skupiny, který jim poskytne kompletní E3 produkt a jejich stále využívat pouze jednu licenci pro tohoto produktu.

> [!TIP]
> Můžete vytvořit několik skupin pro každý plán požadovaných služeb. Například pokud používáte Office 365 Enterprise E1 i Office 365 Enterprise E3 pro vaše uživatele, můžete vytvořit dvě skupiny na licencí Microsoft Analytics síti na pracovišti: ten, který používá E1 předpokladem a druhý, který používá E3. Díky tomu můžete distribuovat doplňku uživatelům E1 a E3 bez použití další licence.

## <a name="license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online"></a>Přiřazení licence selže bez upozornění pro uživatele z důvodu duplicitní proxy adres v systému Exchange Online

Pokud používáte Exchange Online, můžou být někteří uživatelé ve vašem klientovi nesprávně nakonfigurované se stejnou hodnotou adresa proxy serveru. Když na základě skupiny licencí se pokusí o přiřazení licence pro tohoto uživatele, se nezdaří a nezaznamenává k chybě. Omezení verze preview tato funkce je selhání záznam chyby v této instanci a budeme ho před adres *obecné dostupnosti*.

> [!TIP]
> Pokud si všimnete, že někteří uživatelé nepřijala licenci a se nezobrazí žádná chyba zaznamenány pro tyto uživatele, nejdřív zkontrolujte, jestli mají adresa duplicitní proxy.
> Pokud chcete zobrazit, pokud je adresa duplicitní proxy, spusťte následující rutiny prostředí PowerShell pro Exchange Online:
```
Run Get-Recipient | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
```
> Další informace o tomto problému najdete v tématu ["adresu proxy serveru je již používán" chybová zpráva v systému Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). Článek obsahuje také informace na [postup připojení k Exchange Online pomocí vzdáleného prostředí PowerShell](https://technet.microsoft.com/library/jj984289.aspx).

Jakmile vyřešíte potíže s adresu proxy serveru pro příslušné uživatele, ujistěte se, že jste vynutit zpracování licencí ve skupině a ujistěte se, že licence je nyní možné použít.

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>Jak můžete vynutit zpracování licencí ve skupině řešení chyb?

V závislosti na tom, jaké kroky jste provést k vyřešení chyby může být potřeba aktivovat ručně zpracování skupiny k aktualizaci stavu uživatele.

Například pokud některé licence se uvolní odebráním přiřazením přímé licencí od uživatelů, budete muset spustit zpracování skupiny, které se dříve nepodařilo plně všechny členy uživatelské licence. Opětovné zpracování skupinu, přejděte do podokna skupiny, otevřete **licence**a pak vyberte **znovu zpracovat** tlačítka na panelu nástrojů.

## <a name="next-steps"></a>Další postup

Další informace o scénáře pro správu licencí pomocí skupin, naleznete v následujících tématech:

* [Přiřazování licencí pro skupinu v Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Co je na základě skupin licencování v Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Postup migrace na základě skupiny licencí v Azure Active Directory pro jednotlivé licencovaní uživatelé](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory na základě skupin licencí další scénáře](active-directory-licensing-group-advanced.md)
