---
title: Koncepty doménové struktury prostředků pro služby Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, co je doménová struktura prostředků ve službě Azure Active Directory Domain Services a jak prospěje vaší organizaci v hybridním prostředí s omezenými možnostmi ověřování uživatelů nebo problémy se zabezpečením.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: e0e5dde246dbcd5e5cb2e4ae923872a59a539d87
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476399"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Koncepty a funkce doménové struktury prostředků pro službu Azure Active Directory Domain Services

Služba Azure Active Directory Domain Services (AD DS) poskytuje možnosti přihlášení pro starší, místní, obchodní aplikace. Uživatelé, skupiny a heslové hashy místních a cloudových uživatelů se synchronizují se spravovanou doménou Azure AD DS. Tyto synchronizované heslové haly jsou to, co poskytuje uživatelům jednu sadu přihlašovacích údajů, které mohou použít pro místní službu AD DS, Office 365 a Azure Active Directory.

Přestože je zabezpečení a poskytuje další výhody zabezpečení, některé organizace nelze synchronizovat tato uživatelská hesla hash do Azure AD nebo Azure AD DS. Uživatelé v organizaci nemusí znát své heslo, protože používají pouze ověřování pomocí čipových karet. Tato omezení brání některým organizacím používat Azure AD DS ke zvedání a přesouvání místních klasických aplikací do Azure.

Chcete-li tyto potřeby a omezení vyřešit, můžete vytvořit spravovanou doménu Azure AD DS, která používá doménovou strukturu prostředků. Tento koncepční článek vysvětluje, co jsou doménové struktury a jak důvěřují jiným prostředkům, aby poskytovaly metodu zabezpečeného ověřování. Doménové struktury prostředků Azure AD DS jsou aktuálně ve verzi preview.

> [!IMPORTANT]
> Doménové struktury prostředků Azure AD DS aktuálně nepodporují Azure HDInsight nebo Soubory Azure. Výchozí doménové struktury uživatelů Azure AD DS podporují obě tyto další služby.

## <a name="what-are-forests"></a>Co jsou lesy?

*Doménová struktura* je logická konstrukce používaná službou AD DS (AD DS) k *seskupení*jedné nebo více domén . Domény pak ukládají objekty pro uživatele nebo skupiny a poskytují ověřovací služby.

Ve službě Azure AD DS doménová struktura obsahuje jenom jednu doménu. Místní doménové struktury služby AD DS často obsahují mnoho domén. Ve velkých organizacích, zejména po fúzích a akvizicích, můžete skončit s více místními doménovými strukturami, které pak obsahují více domén.

Ve výchozím nastavení se spravovaná doména Azure AD DS vytvoří jako doménová struktura *uživatele.* Tento typ doménové struktury synchronizuje všechny objekty ze služby Azure AD, včetně všech uživatelských účtů vytvořených v místním prostředí služby AD DS. Uživatelské účty můžete přímo ověřit proti spravované doméně Azure AD DS, jako je například pro přihlášení k virtuálnímu počítači připojovanému k doméně. Doménová struktura uživatele funguje, když lze synchronizovat zapisování za ekonomii hesla a uživatelé nepoužívají výhradní metody přihlášení, jako je ověřování pomocí čipových karet.

V doménové struktuře *prostředků* Služby Azure AD DS se uživatelé ověřují prostředně prostředně vztah *důvěryhodnosti* jednosměrné doménové struktury ze svého místního systému AD DS. S tímto přístupem objekty uživatele a heslové hashy nejsou synchronizovány do Služby Azure AD DS. Objekty a pověření uživatele existují pouze v místním systému AD DS. Tento přístup umožňuje podnikům hostovat prostředky a aplikační platformy v Azure, které závisí na klasické ověřování, jako je LDAPS, Kerberos nebo NTLM, ale všechny problémy s ověřováním nebo obavy jsou odebrány. Doménové struktury prostředků Azure AD DS jsou aktuálně ve verzi preview.

Doménové struktury prostředků také poskytují možnost zvedat a posouvat aplikace po jedné součásti. Mnoho starších místních aplikací je vícevrstvých, často pomocí webového serveru nebo front-endu a mnoha komponent souvisejících s databází. Tyto úrovně ztěžují vjednom kroku zvedat a přesouvat celou aplikaci do cloudu. Pomocí doménových struktur prostředků můžete vaši aplikaci postupně zvedat do cloudu, což usnadňuje přesun vaší aplikace do Azure.

## <a name="what-are-trusts"></a>Co jsou svěřenské fondy?

Organizace, které mají více než jednu doménu, často potřebují uživatele pro přístup ke sdíleným prostředkům v jiné doméně. Přístup k těmto sdíleným prostředkům vyžaduje, aby se uživatelé v jedné doméně ověřovali do jiné domény. Chcete-li tyto možnosti ověřování a autorizace mezi klienty a servery v různých doménách, musí existovat *vztah důvěryhodnosti* mezi těmito dvěma doménami.

U vztahů důvěryhodnosti domény ověřovací mechanismy pro každou doménu důvěřují ověřování přicházejícím z jiné domény. Vztahy důvěryhodnosti pomáhají zajistit řízený přístup ke sdíleným prostředkům v doméně prostředků *(důvěřující* doméně) ověřením, že příchozí požadavky na ověření pocházejí od důvěryhodnéautority *(důvěryhodné* domény). Vztahy důvěryhodnosti fungují jako mosty, které umožňují pouze ověřené požadavky na ověření pro cestování mezi doménami.

Způsob, jakým vztah důvěryhodnosti předává požadavky na ověření, závisí na konfiguraci. Vztahy důvěryhodnosti lze nakonfigurovat jedním z následujících způsobů:

* **Jednosměrný** – poskytuje přístup z důvěryhodné domény k prostředkům v důvěřující doméně.
* **Obousměrný** - poskytuje přístup z každé domény k prostředkům v jiné doméně.

Vztahy důvěryhodnosti jsou také konfigurovány tak, aby zpracovávali další vztahy důvěryhodnosti jedním z následujících způsobů:

* **Nepřenositelná** – vztah důvěryhodnosti existuje pouze mezi dvěma doménami partnerů vztahu důvěryhodnosti.
* **Přenositá** – důvěryhodnost se automaticky rozšíří na všechny ostatní domény, kterým důvěřuje některý z partnerů.

V některých případech jsou vztahy důvěryhodnosti automaticky vytvořeny při vytváření domén. Jindy je nutné zvolit typ vztahu důvěryhodnosti a explicitně vytvořit příslušné vztahy. Konkrétní typy používaných vztahů důvěryhodnosti a struktura těchto vztahů důvěryhodnosti závisí na uspořádání adresářové služby Active Directory a na tom, zda v síti existují různé verze systému Windows.

## <a name="trusts-between-two-forests"></a>Vztahy důvěryhodnosti mezi dvěma doménovými strukturami

Vztahy důvěryhodnosti domény v rámci jedné doménové struktury můžete rozšířit do jiné doménové struktury ručním vytvořením jednosměrného nebo obousměrného vztahu důvěryhodnosti doménové struktury. Vztah důvěryhodnosti doménové struktury je přenositý vztah důvěryhodnosti, který existuje pouze mezi kořenovou doménou doménové struktury a druhou kořenovou doménou doménové struktury.

* Jednosměrný vztah důvěryhodnosti doménové struktury umožňuje všem uživatelům v jedné doménové struktuře důvěřovat všem doménám v druhé doménové struktuře.
* Obousměrný vztah důvěryhodnosti doménové struktury tvoří přenositý vztah důvěryhodnosti mezi každou doménou v obou doménových strukturách.

Tranzitita svěřenských fondů doménové struktury je omezena na dva partnery doménové struktury. Vztah důvěryhodnosti doménové struktury se nevztahuje na další doménové struktury důvěryhodné ani jedním z partnerů.

![Diagram důvěryhodnosti doménové struktury z Azure AD DS na místní službu AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

V závislosti na struktuře služby Active Directory organizace můžete vytvořit různé konfigurace důvěryhodnosti domény a doménové struktury. Azure AD DS podporuje jenom jednosměrný vztah důvěryhodnosti doménové struktury. V této konfiguraci prostředky ve službě Azure AD DS můžete důvěřovat všem doménám v místní doménové struktuře.

## <a name="supporting-technology-for-trusts"></a>Podpora technologií pro svěřenské fondy

Vztahy důvěryhodnosti používají různé služby a funkce, například dns k vyhledání řadičů domény v partnerských doménových strukturách. Vztahy důvěryhodnosti jsou také závislé na ověřovacích protokolech NTLM a Kerberos a na mechanismech autorizace a řízení přístupu systému Windows, které pomáhají poskytovat zabezpečenou komunikační infrastrukturu v doménách a doménových strukturách služby Active Directory. Následující služby a funkce pomáhají podporovat úspěšné vztahy důvěryhodnosti.

### <a name="dns"></a>DNS

Služba AD DS potřebuje dns pro umístění a pojmenování řadiče domény (DC). Pro úspěšnou práci služby AD DS je k dispozici následující podpora ze služby DNS:

* Služba překladu názvů, která umožňuje síťovým hostitelům a službám vyhledat řadiče domény.
* Struktura pojmenování, která umožňuje rozlehlé síti odrážet organizační strukturu v názvech domén adresářových služeb.

Obvykle se nasazuje obor názvů domény DNS, který zrcadlí obor názvů domény služby AD DS. Pokud existuje před nasazením služby AD DS existující obor názvů DNS, je obor názvů DNS obvykle rozdělen na oddíly pro službu Active Directory a je vytvořena subdoména DNS a delegování kořenové doménové struktury služby Active Directory. Pro každou podřízenou doménu služby Active Directory jsou pak přidány další názvy domén DNS.

Služba DNS se také používá k podpoře umístění řadičů domény služby Active Directory. Zóny DNS jsou naplněny záznamy o prostředcích DNS, které umožňují síťovým hostitelům a službám vyhledat řadiče domény služby Active Directory.

### <a name="applications-and-net-logon"></a>Aplikace a přihlášení k síti

Aplikace i služba Přihlašování k síti jsou součástmi modelu distribuovaného kanálu zabezpečení systému Windows. Aplikace integrované se systémy Windows Server a Active Directory používají k komunikaci se službou Přihlašování k síti ověřovací protokoly, aby bylo možné vytvořit zabezpečenou cestu, přes kterou může dojít k ověřování.

### <a name="authentication-protocols"></a>Protokoly pro ověřování

Řadiče domény služby Active Directory ověřují uživatele a aplikace pomocí jednoho z následujících protokolů:

* **Ověřovací protokol protokolu Kerberos verze 5**
    * Protokol Kerberos verze 5 je výchozí ověřovací protokol používaný místními počítači se systémem Windows a podporujícími operační systémy jiných výrobců. Tento protokol je určen v jazyce RFC 1510 a je plně integrován se službou Active Directory, blokem zpráv serveru (SMB), protokolem HTTP a vzdáleným voláním procedur (RPC) a také s klientskými a serverovými aplikacemi, které tyto protokoly používají.
    * Při použití protokolu Kerberos server nemusí kontaktovat řadič domény. Místo toho klient získá lístek pro server vyžádáním z řadiče domény v doméně účtu serveru. Server pak ověří lístek bez konzultace s jiným orgánem.
    * Pokud některý počítač zapojený do transakce nepodporuje protokol Kerberos verze 5, použije se protokol NTLM.

* **Ověřovací protokol NTLM**
    * Protokol NTLM je klasický síťový ověřovací protokol používaný staršími operačními systémy. Z důvodů kompatibility jej používají domény služby Active Directory ke zpracování požadavků na ověřování v síti, které pocházejí z aplikací určených pro dřívější klienty a servery se systémem Windows a operačních systémů jiných výrobců.
    * Při použití protokolu NTLM mezi klientem a serverem musí server kontaktovat službu ověřování domény na řadiči domény a ověřit pověření klienta. Server ověřuje klienta předáním pověření klienta do řadiče domény klienta v doméně klientského účtu.
    * Pokud jsou dvě domény nebo doménové struktury služby Active Directory propojeny vztahem důvěryhodnosti, mohou být požadavky na ověření provedené pomocí těchto protokolů směrovány za účelem poskytnutí přístupu k prostředkům v obou doménových strukturách.

## <a name="authorization-and-access-control"></a>Autorizace a řízení přístupu

Technologie autorizace a důvěryhodnosti spolupracují na zajištění zabezpečené komunikační infrastruktury v doménách nebo doménových strukturách služby Active Directory. Autorizace určuje, jakou úroveň přístupu má uživatel k prostředkům v doméně. Vztahy důvěryhodnosti usnadňují autorizaci uživatelů mezi doménami tím, že poskytují cestu k ověřování uživatelů v jiných doménách, aby mohly být jejich požadavky na sdílené prostředky v těchto doménách autorizovány.

Pokud je požadavek na ověření podaný v důvěřující doméně ověřen důvěryhodnou doménou, je předán cílovému prostředku. Cílový prostředek pak určuje, zda má být autorizován konkrétní požadavek uživatele, služby nebo počítače v důvěryhodné doméně na základě konfigurace řízení přístupu.

Vztahy důvěryhodnosti poskytují tento mechanismus k ověření požadavků na ověření, které jsou předány důvěřující doméně. Mechanismy řízení přístupu v počítači prostředků určují konečnou úroveň přístupu uděleného žadateli v důvěryhodné doméně.

## <a name="next-steps"></a>Další kroky

Další informace o vztahu důvěryhodnosti najdete v tématu [Jak fungují vztahy důvěryhodnosti doménové struktury ve službě Azure AD DS?][concepts-trust]

Pokud chcete začít s vytvářením spravované domény Azure AD DS s doménovou doménovou strukturu prostředků, přečtěte si informace [o vytvoření a konfiguraci spravované domény Služby Azure AD DS][tutorial-create-advanced]. Potom můžete [vytvořit odchozí vztah důvěryhodnosti doménové struktury pro místní doménu (náhled).][create-forest-trust]

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
