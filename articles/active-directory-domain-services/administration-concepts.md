---
title: Koncepty správy pro Azure AD Domain Services | Microsoft Docs
description: Informace o tom, jak spravovat Azure Active Directory Domain Services spravovanou doménu a chování uživatelských účtů a hesel
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: iainfou
ms.openlocfilehash: f239bab48e732755361fe734fdc24b37d3823c63
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481023"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Koncepce správy uživatelských účtů, hesel a správy v Azure Active Directory Domain Services

Když vytváříte a spouštíte spravovanou doménu Azure Active Directory Domain Services (služba AD DS), v porovnání s tradičním místním služba AD DS prostředím dochází k nějakým rozdílům v chování. V Azure služba AD DS používáte stejné nástroje pro správu jako samoobslužné domény, ale nemůžete získat přímý přístup k řadičům domény (DC). V závislosti na zdroji vytváření uživatelských účtů je také několik rozdílů v chování zásad hesel a hodnot hash hesel.

Tento koncepční článek podrobně popisuje, jak spravovat spravovanou doménu Azure služba AD DS a různé chování uživatelských účtů v závislosti na tom, jak se vytváří.

## <a name="domain-management"></a>Správa domén

V Azure služba AD DS jsou řadiče domény (DCs), které obsahují všechny prostředky, jako jsou uživatelé a skupiny, přihlašovací údaje a zásady, součástí spravované služby. V případě redundance se dva řadiče domény vytvoří jako součást spravované domény Azure služba AD DS. K těmto řadičům domény se nemůžete přihlásit, abyste mohli provádět úlohy správy. Místo toho vytvoříte virtuální počítač pro správu, který je připojený k spravované doméně Azure služba AD DS, a pak nainstalujete běžné nástroje pro správu služba AD DS. Můžete použít Centrum správy služby Active Directory nebo moduly snap-in konzoly MMC (Microsoft Management Console), jako jsou například objekty DNS nebo Zásady skupiny.

## <a name="user-account-creation"></a>Vytvoření uživatelského účtu

Uživatelské účty je možné v Azure služba AD DS vytvořit několika způsoby. Většina uživatelských účtů je synchronizovaná ve službě Azure AD, která může také zahrnovat uživatelský účet synchronizovaný z místního prostředí služba AD DS. Účty můžete také ručně vytvořit přímo v Azure služba AD DS. Některé funkce, jako je například počáteční synchronizace hesel nebo zásady hesel, se chovají různě v závislosti na tom, jak a kde jsou vytvářeny uživatelské účty.

* Uživatelský účet může být synchronizovaný v rámci služby Azure AD. Patří sem pouze cloudové uživatelské účty vytvořené přímo v Azure AD a hybridní uživatelské účty synchronizované z místního služba AD DS prostředí pomocí Azure AD Connect.
    * Většina uživatelských účtů ve službě Azure služba AD DS se vytváří prostřednictvím procesu synchronizace z Azure AD.
* Uživatelský účet se dá ručně vytvořit ve spravované doméně Azure služba AD DS a neexistuje v Azure AD.
    * Pokud potřebujete vytvořit účty služeb pro aplikace, které běží jenom v Azure služba AD DS, můžete je ručně vytvořit ve spravované doméně. Vzhledem k jednosměrné synchronizaci z Azure AD se uživatelské účty vytvořené v Azure služba AD DS nesynchronizují zpátky do Azure AD.

## <a name="password-policy"></a>Zásady hesel

Azure služba AD DS obsahuje výchozí zásady pro hesla, které definují nastavení pro věci, jako je uzamčení účtu, maximální stáří hesla a složitost hesla. Nastavení, jako je zásada uzamčení účtů, se vztahuje na všechny uživatele v Azure služba AD DS bez ohledu na to, jak byl uživatel vytvořen, jak je uvedeno v předchozí části. Některá nastavení, jako je minimální délka hesla a složitost hesla, se vztahují jenom na uživatele vytvořené přímo v Azure služba AD DS.

Můžete vytvořit vlastní zásady hesel a přepsat tak výchozí zásady v Azure služba AD DS. Tyto vlastní zásady pak můžete podle potřeby použít na konkrétní skupiny uživatelů.

Další informace o rozdílech v tom, jak se používají zásady hesel v závislosti na zdroji vytváření uživatelů, najdete v tématu [zásady hesel a uzamčení účtů ve spravovaných doménách][password-policy].

## <a name="password-hashes"></a>Hodnoty hash hesel

K ověřování uživatelů ve spravované doméně služba AD DS Azure potřebuje hodnoty hash hesel ve formátu, který je vhodný pro ověřování pomocí protokolu NTLM (NT LAN Manager) a Kerberos. Azure AD negeneruje nebo ukládá hodnoty hash hesel ve formátu, který je vyžadován pro ověřování protokolem NTLM nebo Kerberos, dokud nepovolíte služba AD DS Azure pro vašeho tenanta. Z bezpečnostních důvodů Azure AD také neukládá přihlašovací údaje hesla ve formě nešifrovaných textů. Proto služba Azure AD nemůže automaticky generovat tyto hodnoty hash hesla NTLM nebo Kerberos na základě stávajících přihlašovacích údajů uživatelů.

U uživatelských účtů jenom pro Cloud musí uživatelé změnit svoje heslo, aby mohli používat Azure služba AD DS. Tento proces změny hesla způsobí, že se ve službě Azure AD vygenerují a ukládají hodnoty hash hesel pro ověřování pomocí protokolu Kerberos a NTLM.

Pro uživatele synchronizované z místního služba AD DS prostředí pomocí Azure AD Connect [Povolte synchronizaci hodnot hash hesel][hybrid-phs].

> [!IMPORTANT]
> Pokud povolíte Azure služba AD DS pro vašeho tenanta Azure AD, Azure AD Connect synchronizuje jenom starší hodnoty hash hesel. Starší hodnoty hash hesel se nepoužívají, pokud používáte jenom Azure AD Connect k synchronizaci místního prostředí služba AD DS s Azure AD.
>
> Pokud vaše starší aplikace nepoužívají ověřování NTLM nebo jednoduché vazby LDAP, doporučujeme pro Azure služba AD DS zakázat synchronizaci hodnot hash hesel protokolu NTLM. Další informace najdete v tématu [zakázání slabých šifrovacích sad a synchronizace hodnot hash přihlašovacích údajů NTLM][secure-domain].

Po správné konfiguraci se použitelné hodnoty hash hesel ukládají do spravované domény Azure služba AD DS. Pokud odstraníte spravovanou doménu Azure služba AD DS, odstraní se i všechny hodnoty hash hesel uložené v tomto okamžiku. Informace o synchronizovaných přihlašovacích údajích ve službě Azure AD se nedají znovu použít, pokud později vytvoříte Azure služba AD DS spravované domény – musíte znovu nakonfigurovat synchronizaci hodnot hash hesel, aby se znovu ukládaly hodnoty hash hesel. Virtuální počítače připojené k doméně nebo uživatelé nebudou moct hned ověřit – Azure AD potřebuje vygenerovat a uložit hodnoty hash hesel v nové spravované doméně Azure služba AD DS. Další informace najdete v tématu [proces synchronizace hodnot hash hesel pro Azure služba AD DS a Azure AD Connect][azure-ad-password-sync].

> [!IMPORTANT]
> Azure AD Connect by měl být nainstalovaný a nakonfigurovaný jenom pro synchronizaci s místními služba AD DS prostředími. Pro synchronizaci objektů zpět do Azure AD se nepodporuje instalace Azure AD Connect ve spravované doméně Azure služba AD DS.

## <a name="forests-and-trusts"></a>Doménové struktury a vztahy důvěryhodnosti

*Doménová struktura* je logická konstrukce, kterou používá Active Directory Domain Services (služba AD DS) k seskupení jedné nebo více *domén*. Domény pak uchovávají objekty pro uživatele nebo skupiny a poskytují ověřovací služby.

V Azure služba AD DS doménová struktura obsahuje jenom jednu doménu. Místní doménové struktury služba AD DS často obsahují mnoho domén. Ve velkých organizacích, zejména po fúzích a akvizicích, můžete mít několik místních doménových struktur, které každý z nich obsahuje víc domén.

Ve výchozím nastavení je spravovaná doména Azure služba AD DS vytvořená jako doménová struktura *uživatelů* . Tento typ doménové struktury synchronizuje všechny objekty z Azure AD, včetně všech uživatelských účtů vytvořených v místním služba AD DS prostředí. Uživatelské účty se můžou přímo ověřovat proti spravované doméně Azure služba AD DS, například pro přihlášení k virtuálnímu počítači připojenému k doméně. Doménová struktura uživatelů funguje, když je možné synchronizovat hodnoty hash hesla a uživatelé nepoužívají exkluzivní metody přihlašování, jako je ověřování pomocí čipové karty.

V doménové struktuře *prostředků* Azure služba AD DS se uživatelé ověřují pomocí jednosměrné *důvěryhodnosti* doménové struktury ze své místní služba AD DS. S tímto přístupem se uživatelské objekty a hodnoty hash hesel nesynchronizují do Azure služba AD DS. Uživatelské objekty a přihlašovací údaje existují pouze v místních služba AD DS. Tento přístup umožňuje podnikům hostovat prostředky a aplikační platformy v Azure, které jsou závislé na klasických ověřováních, jako jsou protokoly LDAP, Kerberos nebo NTLM, ale všechny problémy s ověřováním nebo obavy se odeberou. Doménové struktury prostředků Azure služba AD DS jsou momentálně ve verzi Preview.

Další informace o typech doménové struktury v Azure služba AD DS najdete v tématu [co jsou doménové struktury prostředků?][concepts-forest] a [jak vztahy důvěryhodnosti doménové struktury fungují v Azure služba AD DS?][concepts-trust]

## <a name="next-steps"></a>Další kroky

Začněte tím, [že vytvoříte Azure služba AD DS spravovanou doménu][create-instance].

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
