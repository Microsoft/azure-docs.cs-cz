---
title: Řešení potíží s přiřazením licencí skupiny – Azure Active Directory | Microsoft Docs
description: Jak identifikovat a vyřešit problémy s přiřazením licencí při použití Azure Active Directory licencování na základě skupin
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b24989c32c0c385f8ad41d73dacba41bb7eef79
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173239"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identifikace a řešení problémů s přiřazením licencí pro skupinu v Azure Active Directory

Licencování na základě skupin v Azure Active Directory (Azure AD) zavádí koncept uživatelů v chybovém stavu licencování. V tomto článku vyvysvětlíme důvody, proč můžou uživatelé skončit v tomto stavu.

Pokud přiřadíte licence přímo jednotlivým uživatelům, bez použití licencování na základě skupin, operace přiřazení může selhat. Například při spuštění rutiny prostředí PowerShell `Set-MsolUserLicense` v uživatelském systému může být rutina neúspěšná z mnoha důvodů, které souvisí s obchodní logikou. Může se například jednat o nedostatečný počet licencí nebo o konflikt mezi dvěma plány služeb, které se nedají přiřadit ve stejnou dobu. Problém se okamžitě nahlásí zpátky.

Pokud používáte licencování na základě skupin, může dojít k těmto chybám, ale dojde na pozadí, zatímco služba Azure AD přiřazuje licence. Z tohoto důvodu chyby nemůžete okamžitě sdělit. Místo toho se zaznamenávají na objekt uživatele a pak se nahlásí prostřednictvím portálu pro správu. Původní záměr na licenci uživatele není nikdy ztracen, ale je zaznamenán v chybovém stavu pro budoucí šetření a řešení.

## <a name="find-license-assignment-errors"></a>Najít Chyby přiřazení licencí

### <a name="to-find-users-in-an-error-state-in-a-group"></a>Vyhledání uživatelů v chybovém stavu ve skupině

1. Otevřete skupinu na stránce s přehledem a vyberte **licence**. Pokud se v chybovém stavu nacházejí nějaké uživatele, zobrazí se oznámení.

   ![Zpráva o skupině a chybách oznámení](./media/licensing-groups-resolve-problems/group-error-notification.png)

1. Vyberte oznámení a otevřete tak seznam všech ovlivněných uživatelů. Můžete vybrat jednotlivé uživatele jednotlivě a zobrazit tak další podrobnosti.

   ![seznam uživatelů v chybovém stavu licencování skupin](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

1. Pokud chcete najít všechny skupiny, které obsahují alespoň jednu chybu, v okně **Azure Active Directory** vyberte **licence** a pak vyberte **Přehled**. Informační pole se zobrazí, když skupiny vyžadují vaši pozornost.

   ![Přehled a informace o skupinách v chybovém stavu](./media/licensing-groups-resolve-problems/group-errors-widget.png)

1. Zaškrtnutím políčka zobrazíte seznam všech skupin s chybami. Pro další podrobnosti můžete vybrat každou skupinu.

   ![Přehled a seznam skupin s chybami](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)

V následujících částech najdete popis každého potenciálního problému a způsob, jak ho vyřešit.

## <a name="not-enough-licenses"></a>Nedostatek licencí

**Problém:** Pro jeden z produktů, které jsou ve skupině zadané, není k dispozici dostatek licencí. Musíte si buď koupit další licence na produkt, nebo uvolnit nepoužívané licence od jiných uživatelů nebo skupin.

Pokud chcete zjistit, kolik licencí je dostupných, přejděte na **Azure Active Directory**  >  **licence**  >  **všechny produkty**.

Pokud chcete zjistit, kteří uživatelé a skupiny pracují s licencemi, vyberte produkt. V části **licencovaní uživatelé** se zobrazí seznam všech uživatelů, kteří mají licence přiřazené přímo nebo přes jednu nebo více skupin. V části **licencované skupiny** se zobrazí všechny skupiny, které mají přiřazené produkty.

**PowerShell:** Rutiny prostředí PowerShell hlásí tuto chybu jako _CountViolation_.

## <a name="conflicting-service-plans"></a>Konfliktní plány služeb

**Problém:** Jeden z produktů uvedených ve skupině obsahuje plán služby, který je v konfliktu s jiným plánem služby, který je už přiřazený k uživateli přes jiný produkt. Některé plány služeb jsou nakonfigurovány způsobem, že je nelze přiřadit stejnému uživateli jako jiný související plán služby.

Představte si následující příklad. Uživatel má licenci pro Office 365 Enterprise *E1* přiřazenou přímo se všemi povolenými plány. Uživatel se přidal do skupiny, která má přiřazený produkt Office 365 Enterprise *E3* . Produkt E3 obsahuje plány služeb, které se nemůžou překrývat s plány, které jsou zahrnuté v E1, takže přiřazení skupinových licencí se nezdařilo s chybou "konfliktní plány služeb". V tomto příkladu jsou konfliktní plány služby:

- SharePoint Online (plán 2) je v konfliktu se SharePointem Online (plán 1).
- Exchange Online (plán 2) koliduje s Exchangem Online (plán 1).

Chcete-li tento konflikt vyřešit, je nutné zakázat dva z těchto plánů. Můžete zakázat licenci E1, která je přímo přiřazena uživateli. Nebo je nutné upravit celé přiřazení licence skupiny a zakázat plány v licenci E3. Případně se můžete rozhodnout odebrat licenci E1 od uživatele, pokud je redundantní v kontextu licence E3.

Rozhodnutí o tom, jak vyřešit konfliktní licence k produktu, vždy patří správci. Služba Azure AD automaticky nevyřeší konflikty licencí.

**PowerShell:** Rutiny prostředí PowerShell hlásí tuto chybu jako _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Další produkty závisí na této licenci.

**Problém:** Jeden z produktů uvedených ve skupině obsahuje plán služby, který musí být povolen pro jiný plán služby, v jiném produktu, aby fungoval. K této chybě dochází, když se Azure AD pokusí odebrat základní plán služby. K tomu může dojít například při odebrání uživatele ze skupiny.

Chcete-li tento problém vyřešit, je nutné zajistit, aby byl požadovaný plán stále přiřazený uživatelům prostřednictvím některé jiné metody nebo aby byly pro tyto uživatele zakázány závislé služby. Až to uděláte, můžete si z těchto uživatelů odebrat licenci skupiny.

**PowerShell:** Rutiny prostředí PowerShell hlásí tuto chybu jako _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>Umístění použití není povolené.

**Problém:** Některé služby společnosti Microsoft nejsou k dispozici ve všech umístěních z důvodu místních zákonů a předpisů. Než budete moct přiřadit licenci k uživateli, musíte zadat vlastnost **umístění využití** pro uživatele. Umístění můžete zadat v **User**  >  **Profile**  >  části **Upravit** profil uživatele v Azure Portal.

Když se Azure AD pokusí přiřadit licenci skupiny uživateli, jehož umístění využívání není podporované, dojde k chybě a zaznamená chybu pro uživatele.

Chcete-li tento problém vyřešit, odeberte z licencované skupiny uživatele z nepodporovaných umístění. Případně, pokud aktuální hodnoty umístění použití nezpůsobí skutečné umístění uživatele, můžete je upravit tak, aby byly licence správně přiřazeny (Pokud je nové umístění podporováno).

**PowerShell:** Rutiny prostředí PowerShell hlásí tuto chybu jako _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Když Azure AD přiřadí licence skupiny, všichni uživatelé bez zadaného umístění využívání zdědí umístění adresáře. Doporučujeme, aby správci nastavili správné hodnoty umístění využívání na uživatele předtím, než použijete licencování na základě skupin pro splnění místních zákonů a předpisů.

## <a name="duplicate-proxy-addresses"></a>Duplicitní proxy adresy

Pokud používáte Exchange Online, někteří uživatelé ve vaší organizaci můžou být nesprávně nakonfigurováni se stejnou hodnotou adresy proxy serveru. Když se licencování na základě skupiny pokusí k tomuto uživateli přiřadit licenci, dojde k chybě a zobrazí se zpráva "adresa proxy se už používá".

> [!TIP]
> Pokud chcete zjistit, jestli je k dispozici duplicitní proxy adresa, spusťte následující rutinu prostředí PowerShell v systému Exchange Online:
> ```
> Get-Recipient -ResultSize unlimited | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> Další informace o tomto problému naleznete v části ["adresa proxy se už používá" v Exchangi Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). Článek obsahuje také informace o [tom, jak se připojit k Exchangi Online pomocí vzdáleného prostředí PowerShell](/powershell/exchange/connect-to-exchange-online-powershell?view=exchange-ps).

Po vyřešení všech problémů s adresou proxy pro ovlivněné uživatele nezapomeňte vynutit zpracování licencí ve skupině, aby bylo zajištěno, že se licence budou nyní moci použít.

## <a name="azure-ad-mail-and-proxyaddresses-attribute-change"></a>Změna atributu pro poštu a ProxyAddresses Azure AD

**Problém:** Když aktualizujete přiřazení licence pro uživatele nebo skupinu, může se stát, že se změní atribut Azure AD mail a ProxyAddresses některých uživatelů.

Aktualizace přiřazení licence u uživatele způsobí aktivaci výpočtu adres proxy, což může měnit atributy uživatele. Chcete-li pochopit přesný důvod změny a vyřešit tento problém, přečtěte si článek o [tom, jak se ve službě Azure AD naplní atribut proxyAddresses](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

## <a name="licenseassignmentattributeconcurrencyexception-in-audit-logs"></a>LicenseAssignmentAttributeConcurrencyException v protokolech auditu

**Problém:** Uživatel má LicenseAssignmentAttributeConcurrencyException pro přiřazení licence v protokolech auditu.
Když se licencování na základě skupiny pokusí zpracovat souběžné přiřazení licence stejné licence uživateli, tato výjimka se zaznamená na uživatele. K tomu obvykle dochází v případě, že je uživatel členem více skupin se stejnou přiřazenou licencí. Služba Azure AD se znovu pokusí zpracovat uživatelskou licenci a tento problém se vyřeší. Od zákazníka není vyžadována žádná akce k vyřešení tohoto problému.

## <a name="more-than-one-product-license-assigned-to-a-group"></a>Pro skupinu se přiřadilo víc než jedna licence k produktu.

Skupině můžete přiřadit více než jednu licenci na produkt. Můžete například přiřadit sadu Office 365 Enterprise E3 a Enterprise Mobility + Security skupině a snadno tak povolit všechny zahrnuté služby pro uživatele.

Služba Azure AD se pokusí přiřadit všechny licence, které jsou zadané ve skupině pro každého uživatele. Pokud Azure AD nemůže přiřadit jeden z produktů kvůli problémům s obchodní logikou, nepřiřazují žádné další licence ve skupině. Příkladem je, že nejsou k dispozici dostatečná licence pro všechny, nebo pokud dojde ke konfliktům s jinými službami, které jsou na uživateli povoleny.

Můžete se podívat na uživatele, kteří se nepodařilo získat přiřazení, a ověřit, které produkty jsou tímto problémem ovlivněny.

## <a name="when-a-licensed-group-is-deleted"></a>Při odstranění licencované skupiny

Aby bylo možné skupinu odstranit, je nutné odebrat všechny licence přiřazené ke skupině. Odebrání licencí ze všech uživatelů ve skupině ale může nějakou dobu trvat. Když odeberete přiřazení licencí ze skupiny, může dojít k chybám, pokud má uživatel přiřazenou závislou licenci, nebo pokud dojde k problému s konfliktem adres proxy, který zakazuje odebrání licence. Pokud má uživatel licenci, která je závislá na licenci, která se odebírá z důvodu odstranění skupiny, přiřazení licence pro uživatele se převede ze zděděné na přímo.

Představte si třeba skupinu s povoleným Office 365 E3/E5 přiřazeným k plánu služby Skype pro firmy. Představte si taky, že několik členů skupiny má přímo přiřazené licence zvukové konference. Při odstranění skupiny se licencování na základě skupin pokusí odebrat sadu Office 365 E3/E5 od všech uživatelů. Vzhledem k tomu, že zvukové konference jsou závislé na Skypu pro firmy, pro všechny uživatele, kteří mají přiřazenou zvukovou konferenci, budou licencování na základě skupin převedeny licence Office 365 E3/E5 na přiřazení přímých licencí.

## <a name="manage-licenses-for-products-with-prerequisites"></a>Správa licencí pro produkty s požadavky

Mezi některé online produkty Microsoftu patří *Doplňky*. Aby bylo možné přiřadit licenci, vyžaduje doplněk doplňky, aby bylo možné povolit uživatele nebo skupinu. V případě licencování na základě skupin vyžaduje systém, aby ve stejné skupině existovaly i plány služby pro splnění požadavků i doplňku. K tomu je potřeba zajistit, aby všichni uživatelé, kteří jsou přidáni do skupiny, mohli získat plně funkční produkt. Představme si následující příklad:

Microsoft Workplace Analytics je doplněk k produktu. Obsahuje jeden plán služby se stejným názvem. Tento plán služby můžeme přiřadit jenom uživateli nebo skupině, když je přiřazený taky jeden z následujících požadavků:

- Exchange Online (plán 1)
- Exchange Online (plán 2)

Pokud se pokusíme tento produkt přiřadit vlastní skupině, portál vrátí zprávu s oznámením. Když vybereme podrobnosti položky, zobrazí se tato chybová zpráva:

  Operace licence se nezdařila. Před přidáním nebo odebráním závislé služby se ujistěte, že skupina obsahuje potřebné služby. **Služba Microsoft Workplace Analytics vyžaduje, aby byla povolená i Exchange Online (plán 2).**"

Aby bylo možné přiřadit licenci k tomuto doplňku ke skupině, je nutné zajistit, aby skupina obsahovala také plán požadované služby. Můžeme třeba aktualizovat existující skupinu, která už obsahuje úplný produkt Office 365 E3 a pak do něj přidat doplněk.

Je také možné vytvořit samostatnou skupinu, která bude obsahovat pouze minimální požadované produkty k tomu, aby doplněk pracoval. Dá se použít k licencování jenom vybraných uživatelů pro daný produkt. Na základě předchozího příkladu byste přiřadili následující produkty do stejné skupiny:

- Office 365 Enterprise E3 s povoleným plánem služby Exchange Online (plán 2)
- Microsoft Workplace Analytics

Od této chvíle budou všichni uživatelé přidaní do této skupiny využívat jednu licenci produktu E3 a jednu licenci na produkt pro analýzu pracoviště. Současně můžou být tito uživatelé členy jiné skupiny, která jim poskytne úplný produkt E3 a pořád pro tento produkt spotřebovává jenom jednu licenci.

> [!TIP]
> Pro každý plán služby můžete vytvořit více skupin. Pokud pro vaše uživatele používáte například sadu Office 365 Enterprise E1 a Office 365 Enterprise E3, můžete vytvořit dvě skupiny k licencování Microsoft Workplace Analytics: jeden, který používá E1 jako součást a druhý, který používá E3. To vám umožní distribuovat doplněk do uživatelů E1 a E3 bez využívání dalších licencí.

## <a name="force-group-license-processing-to-resolve-errors"></a>Vynutit při řešení chyb zpracování licencí skupin

V závislosti na tom, jaké kroky jste udělali k vyřešení chyb, může být nutné ručně aktivovat zpracování skupiny, aby se aktualizoval stav uživatele.

Pokud například uvolníte některé licence odebráním přímých přiřazení licencí od uživatelů, musíte aktivovat zpracování skupin, které dříve nedokázaly plně licencovat všechny členy uživatelů. Chcete-li znovu zpracovat skupinu, v podokně skupiny otevřete položku **licence** a pak na panelu nástrojů vyberte tlačítko znovu **zpracovat** .

## <a name="force-user-license-processing-to-resolve-errors"></a>Vynuťte zpracování uživatelských licencí pro řešení chyb.

V závislosti na tom, jaké kroky jste udělali k vyřešení chyb, může být nutné ručně aktivovat zpracování uživatele, aby se aktualizoval stav uživatele.

Například po vyřešení problému duplicitních adres proxy serveru pro ovlivněného uživatele je třeba aktivovat zpracování uživatele. Chcete-li uživatele znovu zpracovat, v podokně uživatele otevřete položku **licence** a pak vyberte tlačítko znovu **zpracovat** na panelu nástrojů.

## <a name="next-steps"></a>Další kroky

Další informace o dalších scénářích správy licencí prostřednictvím skupin najdete v následujících tématech:

* [Co je licencování na základě skupin v Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Přiřazení licencí ke skupině v Azure Active Directory](./licensing-groups-assign.md)
* [Postup migrace jednotlivě licencovaných uživatelů na licencování na základě skupin v Azure Active Directory](licensing-groups-migrate-users.md)
* [Postup migrace uživatelů mezi licencemi k produktu pomocí licencování na základě skupin v Azure Active Directory](licensing-groups-change-licenses.md)
* [Další scénáře licencování na základě skupin v Azure Active Directory](./licensing-group-advanced.md)
* [Příklady prostředí PowerShell pro licencování na základě skupin v Azure Active Directory](licensing-ps-examples.md)