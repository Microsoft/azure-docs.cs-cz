---
title: Řízení účtů místních služeb | Azure Active Directory
description: Průvodce vytvořením a spuštěním procesu životního cyklu účtu pro účty služeb
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ad7cf7fe2ca1ddcb592e895014b1d956e55e1b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557365"
---
# <a name="governing-on-premises-service-accounts"></a>Řízení účtů místních služeb

Existují čtyři typy místních účtů služeb ve službě Windows Active Directory:

* [Skupinové účty spravované služby](service-accounts-group-managed.md) (účty gMSA)

* [samostatné účty spravované služby](service-accounts-standalone-managed.md) (sMSAs)

* [Účty počítačů](service-accounts-computer.md)

* [Uživatelské účty fungující jako účty služeb](service-accounts-user-on-premises.md)


Je důležité, aby účty služeb byly úzce řízeny: 

* Chraňte účty služby na základě jejich požadavků na případy použití a účelu.

* Spravujte životní cyklus účtů služeb a jejich přihlašovací údaje.

* Posuzuje účty služeb na základě rizika, která budou vystavena, a oprávnění, která provedla, 

* Zajistěte, aby služba Active Directory a Azure Active Directory neprošly žádnými zastaralými účty služeb se potenciálně nedostatku oprávnění.

## <a name="principles-for-creating-a-new-service-account"></a>Principy vytvoření nového účtu služby

Při vytváření nového účtu služby použijte následující kritéria.

| Principy| Požadavky | 
| - |- | 
| Mapování účtu služby| Vytvořte vazbu mezi účtem služby na jednu službu, aplikaci nebo skript. |
| Vlastnictví| Ujistěte se, že existuje vlastník, který žádá a předpokládá zodpovědnost za účet. |
| Obor| Definujte rozsah jasně a předvídat dobu trvání použití pro účet služby. |
| Účel| Vytvořte účty služeb pro jeden konkrétní účel. |
| Privilege| Použijte princip nejnižší úrovně oprávnění pomocí: <br>Nikdy je nepřiřazujte do předdefinovaných skupin, jako jsou správci.<br> Odebrání oprávnění místního počítače, pokud je to vhodné.<br>Přizpůsobení přístupu a použití delegování služby Active Directory pro přístup k adresáři.<br>Použití podrobných přístupových oprávnění.<br>Nastavení vypršení platnosti účtů a omezení na základě umístění u uživatelských účtů služeb |
| Monitorování a audit – použití| Monitorujte přihlašovací údaje a ujistěte se, že odpovídají zamýšlenému využití. Nastavte výstrahy pro použití neobvyklé. |

### <a name="enforce-least-privilege-for-user-accounts-and-limit-account-overuse"></a>Vysazení minimálního oprávnění pro uživatelské účty a omezení nadměrného využití účtu

U uživatelských účtů používaných jako účty služeb použijte následující nastavení:

* [**Vypršení platnosti účtu**](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps): Nastavte účet služby tak, aby po dobu kontroly automaticky vyprší platnost nastaveného času, pokud se nezjistí, že by měl pokračovat.

*  **LogonWorkstations**: omezte oprávnění, kde se může účet služby přihlásit. Pokud se spustí místně na počítači a přistupuje pouze k prostředkům v tomto počítači, omezte jeho přihlášení na jiné místo.

* [**Nelze změnit heslo**](/powershell/module/addsadministration/set-aduser): zabrání účtu služby změnit jeho vlastní heslo nastavením parametru na hodnotu false.

 
## <a name="build-a-lifecycle-management-process"></a>Sestavení procesu správy životního cyklu

Pokud chcete zachovat zabezpečení účtů služeb, musíte je spravovat od doby, kdy je potřeba určit, dokud nebudou vyřazeny z provozu. 

Pro správu životního cyklu účtů služeb použijte následující postup:

1. Shromažďovat informace o využití účtu
1. Zprovoznění účtu služby a aplikace do databáze správy konfigurace (CMDB)
1. Provádění posouzení rizik nebo formální přezkoumání
1. Vytvořte účet služby a použijte omezení.
1. Naplánujte a proveďte opakované recenze. Podle potřeby upravte oprávnění a rozsahy.
1. V případě potřeby účet zajistěte.

### <a name="collect-usage-information-for-the-service-account"></a>Shromažďovat informace o použití účtu služby

Shromážděte příslušné firemní informace pro každý účet služby. V níže uvedené tabulce jsou uvedeny minimální informace, které se mají shromáždit, ale měli byste shromažďovat vše potřebné k tomu, aby se zajistilo, že obchodní případ pro tyto účty je.

| Data| Podrobnosti |
| - | - |
| Vlastník| Uživatel nebo skupina, které mají účet služby |
| Účel| Účel účtu služby |
| Oprávnění (obory)| Očekávaná sada oprávnění |
| Odkazy na databázi správy konfigurace (CMDB)| Účet služby křížového propojení s cílovým skriptem/aplikacemi a vlastníky |
| Riziko| Hodnocení rizik a obchodních dopadů na základě posouzení rizik zabezpečení |
| Doba platnosti| Předpokládaná maximální doba života pro povolení plánování vypršení platnosti účtu nebo recertifikace |


 

V ideálním případě proveďte požadavek na samoobslužnou službu účtu a vyžadovat příslušné informace. Vlastník, který může být aplikací nebo vlastníkem podniku, členem IT nebo vlastníkem infrastruktury. Použití nástroje, jako je Microsoft Forms pro tuto žádost a přidružených informací, usnadňuje portování nástroje pro inventář databáze CMDB, pokud je účet schválený.

### <a name="onboard-service-account-to-cmdb"></a>Připojení účtu služby ke službě CMDB

Shromážděné informace ukládejte do aplikace typu CMDB. Kromě obchodních informací Zahrňte všechny závislosti na jiné infrastruktury, aplikace a procesy.  Toto centrální úložiště vám usnadní:

* Vyhodnotit rizika.

* Nakonfigurujte účet služby s požadovanými omezeními.

* Pochopení relevantní funkční závislosti a závislostí zabezpečení.

* Pravidelně kontrolujte zabezpečení a pokračování v nutnosti provádění.

* Obraťte se na vlastníka pro kontrolu, vyřazení a změnu účtu služby.

Vezměte v úvahu účet služby, který se používá ke spuštění webu a který má oprávnění pro připojení k jedné nebo více databázím SQL. Informace uložené ve vaší databázi CMDB pro tento účet služby můžou být:

|Data | Podrobnosti|
| - | - |
| Vlastník, náměstek| Jan Bloom, Anna Mayers |
| Účel| Spusťte webovou stránku pro personální oddělení a připojte se k PERSONÁLNÍmu oddělení databáze. Může zosobnit koncového uživatele při přístupu k databázím. |
| Oprávnění, obory| HR – webový server: místní přihlášení, spuštění webové stránky<br>HR-SQL1: místní přihlášení, přečtěte si veškerou HR * Database<br>HR-SQL2: místní přihlášení, přečtěte si o mzdě * Database |
| Cost Center| 883944 |
| Hodnocené riziko| Úrovně Dopad na firmu: střední; soukromé informace; Úrovně |
| Omezení účtu| Přihlásit se k: jenom výše uvedené servery; Nejde změnit heslo; Zásady MBI-Password; |
| Doba platnosti| neomezený |
| Projít cyklus| Každý rok (od vlastníka, od bezpečnostního týmu, podle ochrany osobních údajů) |

### <a name="perform-risk-assessment-or-formal-review-of-service-account-usage"></a>Provádění posouzení rizik nebo formální kontroly využití účtu služby

Vzhledem k jeho oprávněním a účelu posuzuje riziko, že účet může představovat přidruženou aplikaci nebo službu a k vaší infrastruktuře, pokud dojde k ohrožení zabezpečení. Vezměte v úvahu přímé i nepřímé riziko. 

* K čemu má nežádoucí osoba získat přímý přístup?

* K jakým jiným informacím nebo systémům může mít přístup účet služby?

* Dá se účet použít k udělení dalších oprávnění?

* Jak se dozvíte, kdy se oprávnění mění?

Posouzení rizik, jakmile se pořídí a zdokumentující, může mít dopad na:

* Omezení účtu

* Doba života účtu

* Požadavky na kontrolu účtu (tempo a revidující)

### <a name="create-a-service-account-and-apply-account-restrictions"></a>Vytvoření účtu služby a použití omezení účtu

Účet služby vytvořte jenom poté, co jsou důležité informace popsané ve vaší organizaci CMDB a provedete posouzení rizik. Omezení účtu by měla být zarovnaná na posouzení rizik. V případě potřeby zvažte následující omezení.:

* [Vypršení platnosti účtu](/powershell/module/activedirectory/set-adaccountexpiration?view=winserver2012-ps)

   * U všech uživatelských účtů používaných jako účty služeb definujte reálné a přesné datum ukončení, které se má použít. Tuto možnost nastavte pomocí příznaku "platnost účtu". Další podrobnosti najdete v tématu[ set-ADAccountExpiration](/powershell/module/addsadministration/set-adaccountexpiration). 

* Přihlášení k ([LogonWorkstation](/powershell/module/addsadministration/set-aduser))

* Požadavky [zásad hesel](../../active-directory-domain-services/password-policy.md)

* Vytvoření v [umístění organizační jednotky](/windows-server/identity/ad-ds/plan/delegating-administration-of-account-ous-and-resource-ous) , které zajišťuje správu pouze pro privilegované uživatele

* Nastavte a Shromážděte auditování [, které detekuje změny](/windows/security/threat-protection/auditing/audit-directory-service-changes) účtu služby – a [použití účtu služby](https://www.manageengine.com/products/active-directory-audit/how-to/audit-kerberos-authentication-events.html).

Až budete připraveni do produkčního prostředí, udělte účtu služby zabezpečený přístup. 

### <a name="schedule-regular-reviews-of-service-accounts"></a>Plánování pravidelných přezkoumání účtů služeb

Nastavte běžné recenze účtů služeb klasifikovaných jako střední a vysoké riziko. Recenze by měly zahrnovat: 

* Vlastnická práva k pokračující potřebě účtu a odůvodnění oprávnění a oborů.

* Projděte si týmy ochrany osobních údajů a zabezpečení, včetně vyhodnocení nadřazených a podřízených připojení.

* Data z auditů, které zajišťují, že se používají jenom pro zamýšlené účely

### <a name="deprovision-service-accounts"></a>Zrušit zřízení účtů služby

V procesu zrušení zřízení nejprve odeberte oprávnění a monitorování a pak odeberte účet, pokud je to vhodné.

Zrušit zřízení účtů služby v těchto případech:

* Skript nebo aplikace, pro které byl účet služby vytvořen, je vyřazený.

* Funkce v rámci skriptu nebo aplikace, která je používána pro účet služby (například přístup ke konkrétnímu prostředku), je vyřazena.

* Účet služby byl nahrazen jiným účtem služby.

Po odebrání všech oprávnění použijte tento postup k odebrání účtu.

1. Po zrušení zřízení přidružené aplikace nebo skriptu Sledujte přihlášení a přístup k prostředkům přidružených účtů služeb, abyste měli jistotu, že se nepoužívá v jiném procesu. Pokud si nejste jistí, že už není potřeba, pokračujte na další krok.

2. Zakažte účet služby od přihlášení a ujistěte se, že už není potřeba. Vytvořte si obchodní zásady pro časové účty, které by měly zůstat zakázané.

3. Odstraňte účet služby, jakmile bude splněna zásada zůstat zakázaná. 

   * V případě účty spravované služby ho můžete [odinstalovat](/powershell/module/activedirectory/uninstall-adserviceaccount?view=winserver2012-ps) pomocí PowerShellu nebo ručně odstranit z kontejneru účtu spravované služby.

   * V případě počítačů nebo uživatelských účtů můžete účet odstranit ručně ze služby Active Directory.

## <a name="next-steps"></a>Další kroky
Přečtěte si následující články týkající se zabezpečení účtů služeb.

* [Seznámení s místními účty služeb](service-accounts-on-premises.md)

* [Zabezpečené skupiny účtů spravované služby](service-accounts-group-managed.md)

* [Zabezpečení samostatných účtů spravované služby](service-accounts-standalone-managed.md)

* [Zabezpečení účtů počítačů](service-accounts-computer.md)

* [Zabezpečení uživatelských účtů](service-accounts-user-on-premises.md)

* [Řízení účtů místních služeb](service-accounts-govern-on-premises.md)