---
title: Řešení problémů s přiřazením licencí skupiny – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Jak identifikovat a vyřešit problémy s přiřazením licencí při používání licencí založených na skupinách Služby Azure Active Directory
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddfc4bf7ed3bdf214a44a5dfe03259d32b2f3f94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025700"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identifikace a řešení problémů s přiřazením licencí pro skupinu ve službě Azure Active Directory

Licencování na základě skupinve službě Azure Active Directory (Azure AD) zavádí koncept uživatelů ve stavu chyby licencování. V tomto článku vysvětlujeme důvody, proč uživatelé mohou skončit v tomto stavu.

Při přiřazování licencí přímo jednotlivým uživatelům bez použití skupinových licencí může operace přiřazení selhat. Například při spuštění rutiny prostředí `Set-MsolUserLicense` PowerShell v uživatelském systému, rutina může selhat z mnoha důvodů, které souvisejí s obchodní logikou. Může například existovat nedostatečný počet licencí nebo konflikt mezi dvěma plány služeb, které nelze přiřadit současně. Problém je okamžitě hlášen zpět k vám.

Pokud používáte skupinové licencování, stejné chyby může dojít, ale k nim dochází na pozadí, zatímco služba Azure AD přiřazuje licence. Z tohoto důvodu chyby nelze sdělit okamžitě. Místo toho jsou zaznamenány na objekt uživatele a pak hlášeny prostřednictvím portálu pro správu. Původní záměr licencovat uživatele se nikdy neztratí, ale je zaznamenán v chybovém stavu pro budoucí šetření a řešení.

## <a name="find-license-assignment-errors"></a>Najít chyby přiřazení licence

### <a name="to-find-users-in-an-error-state-in-a-group"></a>Vyhledání uživatelů v chybovém stavu ve skupině

1. Otevřete skupinu na její stránce s přehledem a vyberte **Licence**. Oznámení se zobrazí, pokud jsou v chybovém stavu uživatelé.

   ![Zpráva skupinových a chybových oznámení](./media/licensing-groups-resolve-problems/group-error-notification.png)

1. Výběrem oznámení otevřete seznam všech dotčených uživatelů. Chcete-li zobrazit další podrobnosti, můžete vybrat jednotlivé uživatele jednotlivě.

   ![seznam uživatelů ve stavu chyby licencování skupiny](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

1. Pokud chcete najít všechny skupiny, které obsahují alespoň jednu chybu, vyberte v okně **Azure Active Directory** **licence**a pak vyberte **Přehled**. Informační rámeček se zobrazí, když skupiny vyžadují vaši pozornost.

   ![Přehled a informace o skupinách v chybovém stavu](./media/licensing-groups-resolve-problems/group-errors-widget.png)

1. Zaškrtnutím tohoto políčka zobrazíte seznam všech skupin s chybami. Můžete vybrat každou skupinu pro více podrobností.

   ![Přehled a seznam skupin s chybami](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)

V následujících částech je uveden popis každého potenciálního problému a způsob jeho řešení.

## <a name="not-enough-licenses"></a>Nedostatek licencí

**Problém:** Pro jeden z produktů, který je ve skupině zadán, není k dispozici dostatek licencí. Je třeba zakoupit více licencí pro produkt nebo uvolnit nevyužité licence od jiných uživatelů nebo skupin.

Chcete-li zjistit, kolik licencí je k dispozici, přejděte na azure licence**služby** >  **Active Directory** > **všechny produkty**.

Chcete-li zjistit, kteří uživatelé a skupiny spotřebovávají licence, vyberte produkt. V části **Licencovaní uživatelé**se zobrazí seznam všech uživatelů, kterým byly licence přiřazeny přímo nebo prostřednictvím jedné nebo více skupin. V části **Licencované skupiny**se zobrazí všechny skupiny, které mají tyto produkty přiřazeny.

**Prostředí PowerShell:** Rutiny prostředí PowerShell hlásí tuto chybu jako _CountViolation_.

## <a name="conflicting-service-plans"></a>Konfliktní plány služeb

**Problém:** Jeden z produktů, který je zadán ve skupině obsahuje plán služeb, který je v konfliktu s jiným plánem služeb, který je již přiřazen uživateli prostřednictvím jiného produktu. Některé plány služeb jsou konfigurovány tak, aby je nebylo možné přiřadit stejnému uživateli jako jinému souvisejícímu plánu služeb.

Představte si následující příklad. Uživatel má přímo přiřazenou licenci pro Office 365 Enterprise *E1* s povolenými všemi plány. Uživatel byl přidán do skupiny, ke které je přiřazen produkt Office 365 Enterprise *E3.* Produkt E3 obsahuje plány služeb, které se nemohou překrývat s plány, které jsou zahrnuty v e1, takže přiřazení skupinové licence se nezdaří s chybou "Konfliktní plány služeb". V tomto příkladu jsou konfliktní plány služeb:

- SharePoint Online (plán 2) je v konfliktu se SharePointem Online (plán 1).
- Exchange Online (plán 2) je v konfliktu se Exchange Online (plán 1).

Chcete-li tento konflikt vyřešit, musíte zakázat dva plány. Licenci E1, která je přímo přiřazena uživateli, můžete zakázat. Nebo je třeba upravit celé přiřazení skupinové licence a zakázat plány v licenci E3. Případně se můžete rozhodnout odebrat licenci E1 uživateli, pokud je nadbytečná v kontextu licence E3.

Rozhodnutí o řešení konfliktních licencí produktů vždy patří správci. Azure AD automaticky neřeší konflikty licencí.

**Prostředí PowerShell:** Rutiny prostředí PowerShell hlásí tuto chybu jako _Vzájemně výhradní porušení_.

## <a name="other-products-depend-on-this-license"></a>Ostatní produkty závisí na této licenci

**Problém:** Jeden z produktů, který je zadán ve skupině obsahuje plán služeb, který musí být povolen pro jiný plán služeb, v jiném produktu, aby fungoval. K této chybě dochází, když se Azure AD pokusí odebrat základní plán služeb. K tomu může dojít například při odebrání uživatele ze skupiny.

Chcete-li tento problém vyřešit, musíte se ujistit, že požadovaný plán je stále přiřazen uživatelům prostřednictvím jiné metody nebo že závislé služby jsou pro tyto uživatele zakázány. Poté můžete správně odebrat skupinovou licenci těmto uživatelům.

**Prostředí PowerShell:** Rutiny prostředí PowerShell hlásí tuto chybu jako _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>Umístění využití není povoleno.

**Problém:** Některé služby společnosti Microsoft nejsou k dispozici ve všech lokalitách z důvodu místních zákonů a předpisů. Před přiřazením licence uživateli je nutné zadat vlastnost **Umístění použití** pro uživatele. Umístění můžete zadat v části**Nastavení** **profilu** >  **uživatele** > na webu Azure Portal.

Když se Azure AD pokusí přiřadit skupinovou licenci uživateli, jehož umístění použití není podporováno, selže a zaznamená chybu uživatele.

Chcete-li tento problém vyřešit, odeberte uživatele z nepodporovaných umístění z licencované skupiny. Případně pokud aktuální hodnoty umístění použití nepředstavují skutečné umístění uživatele, můžete je upravit tak, aby licence byly příště správně přiřazeny (pokud je podporováno nové umístění).

**Prostředí PowerShell:** Rutiny prostředí PowerShell hlásí tuto chybu jako _ZakázanéInUsageLocationViolation_.

> [!NOTE]
> Když Azure AD přiřadí skupinové licence, všichni uživatelé bez zadaného umístění použití zdědí umístění adresáře. Doporučujeme, aby správci nastavili správné hodnoty umístění použití uživatelů před použitím skupinových licencí v souladu s místními zákony a předpisy.

## <a name="duplicate-proxy-addresses"></a>Duplicitní adresy proxy

Pokud používáte Exchange Online, někteří uživatelé ve vašem tenantovi může být nesprávně nakonfigurován se stejnou hodnotou adresy proxy. Když se skupinové licencování pokusí přiřadit licenci takovému uživateli, selže a zobrazí se "Proxy adresa je již používána".

> [!TIP]
> Chcete-li zjistit, zda existuje duplicitní adresa proxy serveru, proveďte následující rutinu prostředí PowerShell proti Exchange Online:
> ```
> Get-Recipient -ResultSize unlimited | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> Další informace o tomto problému naleznete [v tématu "Proxy adresa je již používána" chybová zpráva v Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). Článek také obsahuje informace o [tom, jak se připojit k Exchange Online pomocí vzdáleného prostředí PowerShell](https://technet.microsoft.com/library/jj984289.aspx).

Po vyřešení problémů s adresou proxy pro postižené uživatele vynutit zpracování licencí ve skupině a ujistěte se, že licence lze nyní použít.

## <a name="azure-ad-mail-and-proxyaddresses-attribute-change"></a>Změna atributu Azure AD Mail a ProxyAddresses

**Problém:** Při aktualizaci přiřazení licencí na uživatele nebo skupiny, může se zobrazit, že Azure AD Mail a ProxyAddresses atribut některých uživatelů se změní.

Aktualizace přiřazení licence u uživatele způsobí, že se spustí výpočet adresy proxy, což může změnit atributy uživatele. Chcete-li pochopit přesný důvod změny a vyřešit problém, naleznete v tomto článku o [tom, jak proxyAddresses atribut je naplněn a Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

## <a name="licenseassignmentattributeconcurrencyexception-in-audit-logs"></a>LicenseAssignmentAttributeConcurrencyExceptionV v protokolech auditu

**Problém:** Uživatel má LicenseAssignmentAttributeConcurrencyException pro přiřazení licence v protokolech auditování.
Pokud se skupinové licencování pokusí zpracovat souběžné přiřazení stejné licence uživateli, je tato výjimka zaznamenána uživateli. K tomu obvykle dochází, když je uživatel členem více než jedné skupiny se stejnou přiřazenou licencí. AZure AD se pokusí o zpracování uživatelské licence a problém vyřeší. K vyřešení tohoto problému není od zákazníka vyžadována žádná akce.

## <a name="more-than-one-product-license-assigned-to-a-group"></a>Více než jedna licence produktu přiřazená ke skupině

Skupině můžete přiřadit více než jednu licenci produktu. Můžete například přiřadit Office 365 Enterprise E3 a Enterprise Mobility + Security skupině, abyste mohli snadno povolit všechny zahrnuté služby pro uživatele.

Azure AD se pokusí přiřadit všechny licence, které jsou určeny ve skupině pro každého uživatele. Pokud Azure AD nelze přiřadit jeden z produktů z důvodu problémů obchodní logiky, nebude přiřadit další licence ve skupině jeden. Příkladem je, pokud není dostatek licencí pro všechny, nebo pokud existují konflikty s jinými službami, které jsou povoleny pro uživatele.

Můžete zobrazit uživatele, kterým se nepodařilo získat přiřazení, a zkontrolovat, které produkty jsou tímto problémem ovlivněny.

## <a name="when-a-licensed-group-is-deleted"></a>Při odstranění licencované skupiny

Před odstraněním skupiny je nutné odebrat všechny licence přiřazené skupině. Odebrání licencí všem uživatelům ve skupině však může nějakou dobu trvat. Při odebírání přiřazení licencí ze skupiny může dojít k chybám, pokud má uživatel přiřazenou závislou licenci nebo pokud existuje problém s konfliktem proxy adres, který zakazuje odebrání licence. Pokud má uživatel licenci, která je závislá na licenci, která je odebrána z důvodu odstranění skupiny, přiřazení licence uživateli je převedenz zděděné na přímé.

Zvažte například skupinu, která má office 365 E3/E5 přiřazený s povoleným plánem služeb Skypu pro firmy. Představte si také, že několik členů skupiny má přímo přiřazené licence ke zvukovým konferencím. Po odstranění skupiny se licencování na základě skupiny pokusí odebrat Office 365 E3/E5 všem uživatelům. Vzhledem k tomu, že funkce AudioConing je závislá na Skypu pro firmy, pro všechny uživatele s přiřazeným audiokonferencem převede skupinové licencování licence Office 365 E3/E5 na přímé přiřazení licencí.

## <a name="manage-licenses-for-products-with-prerequisites"></a>Správa licencí pro produkty s požadavky

Některé produkty Microsoft Online, které můžete *vlastnit,* jsou doplňky . Doplňky vyžadují, aby byl nezbytný plán služeb povolen pro uživatele nebo skupinu předtím, než jim může být přiřazena licence. U licencí založených na skupinách systém vyžaduje, aby plány služeb předpokladů i doplňkových služeb byly k dispozici ve stejné skupině. To se provádí, aby bylo zajištěno, že všichni uživatelé, kteří jsou přidáni do skupiny, mohou získat plně funkční produkt. Představme si následující příklad:

Microsoft Workplace Analytics je doplňkový produkt. Obsahuje jeden plán služeb se stejným názvem. Tento plán služeb můžeme přiřadit uživateli nebo skupině pouze v případě, že je přiřazen také jeden z následujících předpokladů:

- Exchange Online (plán 1)
- Exchange Online (plán 2)

Pokud se pokusíme přiřadit tento produkt samostatně ke skupině, portál vrátí oznámení. Pokud vybereme podrobnosti o položce, zobrazí se následující chybová zpráva:

  "Licenční operace se nezdařila. Před přidáním nebo odebráním závislé služby se ujistěte, že skupina má potřebné služby. **Služba Microsoft Workplace Analytics vyžaduje, aby byl povolen také Exchange Online (plán 2).**

Chcete-li přiřadit tuto doplňkovou licenci ke skupině, musíme zajistit, aby skupina také obsahovala plán služeb. Můžeme například aktualizovat existující skupinu, která už obsahuje celý produkt Office 365 E3, a pak do ní přidat doplňkový produkt.

Je také možné vytvořit samostatnou skupinu, která obsahuje pouze minimální požadované produkty, aby doplněk fungoval. To může být použit k licenci pouze vybrané uživatele pro add-on produktu. Na základě předchozího příkladu byste ke stejné skupině přiřadili následující produkty:

- Office 365 Enterprise E3 s povoleným pouze plánem služeb Exchange Online (plán 2)
- Microsoft Workplace Analytics

Od této chvíle všichni uživatelé připřidání do této skupiny spotřebovávají jednu licenci produktu E3 a jednu licenci produktu Workplace Analytics. Současně mohou být tito uživatelé členy jiné skupiny, která jim dává plný produkt E3, a stále spotřebovávají pouze jednu licenci pro tento produkt.

> [!TIP]
> Pro každý požadovaný plán služeb můžete vytvořit více skupin. Pokud například pro uživatele používáte Office 365 Enterprise E1 i Office 365 Enterprise E3, můžete vytvořit dvě skupiny, které budou licencovat službu Microsoft Workplace Analytics: jednu, která používá E1 jako předpoklad, a druhou, která používá E3. To umožňuje distribuovat doplněk uživatelům E1 a E3 bez nutnosti využívání dalších licencí.

## <a name="force-group-license-processing-to-resolve-errors"></a>Vynutit zpracování licence skupiny k vyřešení chyb

V závislosti na tom, jaké kroky jste provedli k vyřešení chyb, může být nutné ručně aktivovat zpracování skupiny k aktualizaci stavu uživatele.

Pokud například uvolníte některé licence odebráním přímých přiřazení licencí uživatelům, je třeba spustit zpracování skupin, kterým se dříve nepodařilo plně licencovat všechny členy uživatele. Chcete-li skupinu znovu zpracovat, přejděte do podokna **skupiny,** otevřete licence a vyberte tlačítko **Znovu zpracovat** na panelu nástrojů.

## <a name="force-user-license-processing-to-resolve-errors"></a>Vynutit zpracování uživatelských licencí k vyřešení chyb

V závislosti na tom, jaké kroky jste provedli k vyřešení chyb, může být nutné ručně aktivovat zpracování uživatele k aktualizaci stavu uživatelů.

Například po vyřešení duplicitní proxy adresu problém pro postiženého uživatele, je třeba spustit zpracování uživatele. Chcete-li uživatele znovu zpracovat, přejděte do podokna uživatele, otevřete **licence**a vyberte tlačítko **Znovu zpracovat** na panelu nástrojů.

## <a name="next-steps"></a>Další kroky

Další informace o dalších scénářích správy licencí prostřednictvím skupin najdete v následujících tématech:

* [Co je skupinové licencování ve službě Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Přiřazení licencí ke skupině v Azure Active Directory](licensing-groups-assign.md)
* [Postup migrace jednotlivě licencovaných uživatelů na licencování na základě skupin v Azure Active Directory](licensing-groups-migrate-users.md)
* [Jak migrovat uživatele mezi licencemi produktů pomocí skupinových licencí ve službě Azure Active Directory](licensing-groups-change-licenses.md)
* [Další scénáře licencování na základě skupin v Azure Active Directory](licensing-group-advanced.md)
* [Příklady Prostředí PowerShell pro skupinové licencování ve službě Azure Active Directory](licensing-ps-examples.md)
