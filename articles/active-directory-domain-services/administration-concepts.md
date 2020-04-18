---
title: Koncepty správy pro služby Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Informace o správě spravované domény služby Azure Active Directory Domain Services a chování uživatelských účtů a hesel
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: ba281ffb30801e0ae10cab10ceb95c0a3bffde2d
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640023"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Koncepty správy pro uživatelské účty, hesla a správu ve službě Azure Active Directory Domain Services

Při vytváření a spuštění spravované domény služby Azure Active Directory Domain Services (AD DS) existují určité rozdíly v chování ve srovnání s tradičním místním prostředím služby AD DS. Ve službě Azure AD DS používáte stejné nástroje pro správu jako samospravovanou doménu, ale nemůžete přímo přistupovat k řadičům domény (DC). Existují také některé rozdíly v chování zásad hesel a hash hesel v závislosti na zdroji vytvoření uživatelského účtu.

Tento koncepční článek podrobně popisuje, jak spravovat spravovanou doménu Azure AD DS a různé chování uživatelských účtů v závislosti na způsobu jejich vytvoření.

## <a name="domain-management"></a>Správa domény

Ve službě Azure AD DS jsou součástí spravované služby řadiče domény (CS), které obsahují všechny prostředky, jako jsou uživatelé a skupiny, přihlašovací údaje a zásady. Pro redundanci se vytvoří dva řadiče domény jako součást spravované domény Služby Azure AD DS. K těmto řadičům domény se nemůžete přihlásit a provádět úkoly správy. Místo toho vytvoříte virtuální počítač pro správu, který je spojený se spravovanou doménou Azure AD DS, a pak nainstalujete běžné nástroje pro správu služby AD DS. Můžete například použít moduly snap-in Centra pro správu služby Active Directory nebo konzolu MMC (MMC).

## <a name="user-account-creation"></a>Vytvoření uživatelského účtu

Uživatelské účty lze vytvořit ve službě Azure AD DS několika způsoby. Většina uživatelských účtů se synchronizuje ze služby Azure AD, což může také zahrnovat uživatelský účet synchronizovaný z místního prostředí služby AD DS. Účty můžete také vytvořit ručně přímo ve službě Azure AD DS. Některé funkce, jako je počáteční synchronizace hesel nebo zásady hesel, se chovají odlišně v závislosti na tom, jak a kde jsou uživatelské účty vytvořeny.

* Uživatelský účet lze synchronizovat v z Azure AD. To zahrnuje uživatelské účty pouze pro cloud vytvořené přímo ve službě Azure AD a hybridní uživatelské účty synchronizované z místního prostředí služby AD DS pomocí služby Azure AD Connect.
    * Většina uživatelských účtů ve službě Azure AD DS se vytvoří prostřednictvím procesu synchronizace z Azure AD.
* Uživatelský účet lze ručně vytvořit ve spravované doméně Azure AD DS a neexistuje ve službě Azure AD.
    * Pokud potřebujete vytvořit účty služeb pro aplikace, které běží jenom ve službě Azure AD DS, můžete je ručně vytvořit ve spravované doméně. Jako synchronizace je jedním ze způsobů, od Azure AD, uživatelské účty vytvořené v Azure AD DS nejsou synchronizovány zpět do Azure AD.

## <a name="password-policy"></a>Zásady hesel

Azure AD DS obsahuje výchozí zásady hesel, které definují nastavení pro věci, jako je uzamčení účtu, maximální stáří hesla a složitost hesla. Nastavení, jako je zásady uzamčení účtu platí pro všechny uživatele ve službě Azure AD DS, bez ohledu na to, jak byl uživatel vytvořen, jak je uvedeno v předchozí části. Několik nastavení, jako je minimální délka hesla a složitost hesla, platí pouze pro uživatele vytvořené přímo ve službě Azure AD DS.

Můžete vytvořit vlastní zásady hesla přepsat výchozí zásady v Azure AD DS. Tyto vlastní zásady pak lze použít pro určité skupiny uživatelů podle potřeby.

Další informace o rozdílech v použití zásad hesel v závislosti na zdroji vytvoření uživatele naleznete v tématu [Zásady uzamčení hesla a účtu ve spravovaných doménách][password-policy].

## <a name="password-hashes"></a>Hesel hashe

K ověření uživatelů ve spravované doméně potřebuje služba Azure AD DS hodnotu hash hesel ve formátu, který je vhodný pro nt lan manager (NTLM) a ověřování protokolem Kerberos. Azure AD negeneruje ani ukládat hesla hashe ve formátu, který je vyžadován pro ověřování NTLM nebo Kerberos, dokud nepovolíte Azure AD DS pro vašeho tenanta. Z bezpečnostních důvodů Azure AD také neukládá žádné přihlašovací údaje hesla ve formě prostého textu. Azure AD proto nemůže automaticky generovat tyto hashe ntlm nebo kerberos hesla na základě existujících přihlašovacích údajů uživatelů.

Pro uživatelské účty pouze pro cloud uživatelé musí změnit svá hesla, než budou moci používat Azure AD DS. Tento proces změny hesla způsobí, že hodnot hash hesel pro ověřování protokolů Kerberos a NTLM se vygenerují a uloží ve službě Azure AD. Účet není synchronizován z Azure AD do Azure AD DS, dokud se nezmění heslo.

Pro uživatele synchronizované z místního prostředí služby AD DS pomocí služby Azure AD Connect [povolte synchronizaci hodnot hashe hesel][hybrid-phs].

> [!IMPORTANT]
> Azure AD Connect synchronizuje pouze starší nastavení hashe hesla, když povolíte Azure AD DS pro vašeho klienta Azure AD. Starší nastavení hashe hesla se nepoužívají, pokud používáte azure ad připojení k synchronizaci místního prostředí Služby AD DS s Azure AD.
>
> Pokud starší aplikace nepoužívají ověřování NTLM nebo jednoduché vazby LDAP, doporučujeme zakázat synchronizaci hash hesel NTLM pro Azure AD DS. Další informace naleznete [v tématu Disable weak cipher suites and NTLM credential hash synchronization][secure-domain].

Po vhodně nakonfigurované, použitelné heslo hashes jsou uloženy ve spravované doméně Azure AD DS. Pokud odstraníte spravovanou doménu Azure AD DS, odstraní se také všechny hashe hesel uložené v tomto okamžiku. Informace o synchronizovaných přihlašovacích údajích ve službě Azure AD nelze znovu použít, pokud později vytvoříte spravovanou doménu Služby Azure AD DS – je nutné znovu nakonfigurovat synchronizaci hodnot hash hesla, abyste znovu ukládali hodnothash hesla. Dříve virtuální počítače spojené s doménou nebo uživatelé nebudou moct okamžitě ověřit – Azure AD potřebuje generovat a ukládat hash hesla v nové spravované doméně Azure AD DS. Další informace najdete [v tématu proces synchronizace hash hesla pro Azure AD DS a Azure AD Connect][azure-ad-password-sync].

> [!IMPORTANT]
> Azure AD Connect by se měl nainstalovat a nakonfiguroval jenom pro synchronizaci s místními prostředími služby AD DS. Není podporováno instalace Azure AD Connect ve spravované doméně Azure AD DS k synchronizaci objektů zpět do Azure AD.

## <a name="forests-and-trusts"></a>Doménové struktury a vztahy důvěryhodnosti

*Doménová struktura* je logická konstrukce používaná službou AD DS (AD DS) k *seskupení*jedné nebo více domén . Domény pak ukládají objekty pro uživatele nebo skupiny a poskytují ověřovací služby.

Ve službě Azure AD DS doménová struktura obsahuje jenom jednu doménu. Místní doménové struktury služby AD DS často obsahují mnoho domén. Ve velkých organizacích, zejména po fúzích a akvizicích, můžete skončit s více místními doménovými strukturami, které pak obsahují více domén.

Ve výchozím nastavení se spravovaná doména Azure AD DS vytvoří jako doménová struktura *uživatele.* Tento typ doménové struktury synchronizuje všechny objekty ze služby Azure AD, včetně všech uživatelských účtů vytvořených v místním prostředí služby AD DS. Uživatelské účty můžete přímo ověřit proti spravované doméně Azure AD DS, jako je například pro přihlášení k virtuálnímu počítači připojovanému k doméně. Doménová struktura uživatele funguje, když lze synchronizovat zapisování za ekonomii hesla a uživatelé nepoužívají výhradní metody přihlášení, jako je ověřování pomocí čipových karet.

V doménové struktuře *prostředků* Služby Azure AD DS se uživatelé ověřují prostředně prostředně vztah *důvěryhodnosti* jednosměrné doménové struktury ze svého místního systému AD DS. S tímto přístupem objekty uživatele a heslové hashy nejsou synchronizovány do Služby Azure AD DS. Objekty a pověření uživatele existují pouze v místním systému AD DS. Tento přístup umožňuje podnikům hostovat prostředky a aplikační platformy v Azure, které závisí na klasické ověřování, jako je LDAPS, Kerberos nebo NTLM, ale všechny problémy s ověřováním nebo obavy jsou odebrány. Doménové struktury prostředků Azure AD DS jsou aktuálně ve verzi preview.

Další informace o typech doménových struktur ve službě Azure [How do forest trusts work in Azure AD DS?][concepts-trust] AD DS najdete v tématech [Co jsou doménové struktury prostředků?][concepts-forest]

## <a name="azure-ad-ds-skus"></a>Azure AD DS SKUs

Ve službě Azure AD DS jsou dostupné výkon a funkce založené na skladové jednotce. Při vytváření spravované domény vyberete skladovou položku a po nasazení spravované domény můžete přepnout skladové položky podle toho, jak se změní vaše obchodní požadavky. V následující tabulce jsou uvedeny dostupné sloky a rozdíly mezi nimi:

| Název skladové položky   | Maximální počet objektů | Frekvence zálohování | Maximální počet odchozích vztahů důvěryhodnosti doménové struktury |
|------------|----------------------|------------------|----|
| Standard   | Unlimited            | Každých 7 dní     | 0  |
| Enterprise | Unlimited            | Každé 3 dny     | 5  |
| Premium    | Unlimited            | denně            | 10 |

Před těmito správcovských zařízení Služby Pro ds služby Azure AD bylo použito fakturační model založený na počtu objektů (uživatelských účtů a účtů počítačů) ve spravované doméně Azure AD DS. Na základě počtu objektů ve spravované doméně již neexistuje variabilní cena.

Další informace najdete na [stránce s cenami služby Azure AD DS][pricing].

### <a name="managed-domain-performance"></a>Výkon spravované domény

Výkon domény se liší v závislosti na způsobu implementace ověřování pro aplikaci. Další výpočetní prostředky mohou pomoci zlepšit dobu odezvy dotazu a zkrátit dobu strávenou v operacích synchronizace. S rostoucí úrovní skladové položky se zvyšují výpočetní prostředky, které jsou k dispozici pro spravovanou doménu. Sledujte výkon aplikací a plánujte požadované prostředky.

Pokud vaše firma nebo aplikace požaduje změnu a potřebujete další výpočetní výkon pro spravovanou doménu Azure AD DS, můžete přepnout na jinou skladovou položku.

### <a name="backup-frequency"></a>Frekvence zálohování

Frekvence zálohování určuje, jak často je pořízen snímek spravované domény. Zálohy jsou automatizovaný proces spravovaný platformou Azure. V případě problému se spravovanou doménou vám podpora Azure může pomoci při obnovení ze zálohy. Jako synchronizace dochází jenom jedním způsobem *z* Azure AD, žádné problémy ve spravované doméně Azure AD DS nebude mít vliv na Azure AD nebo místní prostředí a funkce služby AD DS.

S tím, jak se zvyšuje úroveň skladové položky, zvyšuje se frekvence těchto snímků zálohování. Zkontrolujte obchodní požadavky a cíl bodu obnovení (RPO) a určete požadovanou frekvenci zálohování pro spravovanou doménu. Pokud se změní vaše obchodní nebo aplikační požadavky a potřebujete častější zálohování, můžete přepnout na jinou skladovou položku.

### <a name="outbound-forests"></a>Odchozí doménové struktury

Předchozí část podrobně jednosměrné odchozí vztahy důvěryhodnosti doménové struktury ze spravované domény Azure AD DS do místního prostředí služby AD DS (aktuálně ve verzi Preview). Skladová položka určuje maximální počet vztahů důvěryhodnosti doménové struktury, které můžete vytvořit pro spravovanou doménu Služby Azure AD DS. Zkontrolujte požadavky na podnikání a aplikace, abyste zjistili, kolik vztahů důvěryhodnosti skutečně potřebujete, a vyberte příslušnou skladovou položku Služby DS služby Azure AD. Opět platí, že pokud se změní vaše obchodní požadavky a potřebujete vytvořit další vztahy důvěryhodnosti doménové struktury, můžete přepnout na jinou skladovou položku.

## <a name="next-steps"></a>Další kroky

Chcete-li začít, [vytvořte spravovanou doménu Azure AD DS][create-instance].

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
