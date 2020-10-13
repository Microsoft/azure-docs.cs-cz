---
title: Koncepty správy pro Azure AD Domain Services | Microsoft Docs
description: Informace o tom, jak spravovat Azure Active Directory Domain Services spravovanou doménu a chování uživatelských účtů a hesel
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: joflore
ms.openlocfilehash: 95c6a1f24335849fb2d2c4de56b4ed60e8e5f73f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962815"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Koncepce správy uživatelských účtů, hesel a správy v Azure Active Directory Domain Services

Když vytváříte a spouštíte spravovanou doménu Azure Active Directory Domain Services (služba AD DS), v porovnání s tradičním místním služba AD DS prostředím dochází k nějakým rozdílům v chování. V Azure služba AD DS používáte stejné nástroje pro správu jako samoobslužné domény, ale nemůžete získat přímý přístup k řadičům domény (DC). V závislosti na zdroji vytváření uživatelských účtů je také několik rozdílů v chování zásad hesel a hodnot hash hesel.

Tento koncepční článek podrobně popisuje, jak spravovat spravovanou doménu a různé chování uživatelských účtů v závislosti na tom, jak se vytváří.

## <a name="domain-management"></a>Správa domén

Spravovaná doména je obor názvů DNS a odpovídajícího adresáře. Ve spravované doméně jsou řadiče domény (DCs), které obsahují všechny prostředky, jako jsou uživatelé a skupiny, přihlašovací údaje a zásady, součástí spravované služby. V případě redundance se jako součást spravované domény vytvoří dva řadiče domény. K těmto řadičům domény se nemůžete přihlásit, abyste mohli provádět úlohy správy. Místo toho vytvoříte virtuální počítač pro správu, který je připojený ke spravované doméně, a pak nainstalujete své běžné služba AD DS nástroje pro správu. Můžete použít Centrum správy služby Active Directory nebo moduly snap-in konzoly MMC (Microsoft Management Console), jako jsou například objekty DNS nebo Zásady skupiny.

## <a name="user-account-creation"></a>Vytvoření uživatelského účtu

Uživatelské účty je možné ve spravované doméně vytvořit několika způsoby. Většina uživatelských účtů je synchronizovaná ve službě Azure AD, která může také zahrnovat uživatelský účet synchronizovaný z místního prostředí služba AD DS. Účty můžete také ručně vytvořit přímo ve spravované doméně. Některé funkce, jako je například počáteční synchronizace hesel nebo zásady hesel, se chovají různě v závislosti na tom, jak a kde jsou vytvářeny uživatelské účty.

* Uživatelský účet může být synchronizovaný v rámci služby Azure AD. Patří sem pouze cloudové uživatelské účty vytvořené přímo v Azure AD a hybridní uživatelské účty synchronizované z místního služba AD DS prostředí pomocí Azure AD Connect.
    * Většina uživatelských účtů ve spravované doméně se vytvoří prostřednictvím procesu synchronizace z Azure AD.
* Uživatelský účet se dá ručně vytvořit ve spravované doméně a neexistuje ve službě Azure AD.
    * Pokud potřebujete vytvořit účty služeb pro aplikace, které běží jenom ve spravované doméně, můžete je ručně vytvořit ve spravované doméně. Protože synchronizace je ze služby Azure AD jedním ze způsobů, uživatelské účty vytvořené ve spravované doméně se nesynchronizují zpátky do Azure AD.

## <a name="password-policy"></a>Zásady hesel

Azure služba AD DS obsahuje výchozí zásady pro hesla, které definují nastavení pro věci, jako je uzamčení účtu, maximální stáří hesla a složitost hesla. Nastavení, jako je zásada uzamčení účtu, se vztahují na všechny uživatele ve spravované doméně bez ohledu na to, jak byl uživatel vytvořen, jak je uvedeno v předchozí části. Některá nastavení, jako je minimální délka hesla a složitost hesla, se vztahují jenom na uživatele vytvořené přímo ve spravované doméně.

Můžete vytvořit vlastní zásady hesel a přepsat tak výchozí zásady ve spravované doméně. Tyto vlastní zásady pak můžete podle potřeby použít na konkrétní skupiny uživatelů.

Další informace o rozdílech v tom, jak se používají zásady hesel v závislosti na zdroji vytváření uživatelů, najdete v tématu [zásady hesel a uzamčení účtů ve spravovaných doménách][password-policy].

## <a name="password-hashes"></a>Hodnoty hash hesel

K ověřování uživatelů ve spravované doméně služba AD DS Azure potřebuje hodnoty hash hesel ve formátu, který je vhodný pro ověřování pomocí protokolu NTLM (NT LAN Manager) a Kerberos. Azure AD negeneruje nebo ukládá hodnoty hash hesel ve formátu, který je vyžadován pro ověřování protokolem NTLM nebo Kerberos, dokud nepovolíte služba AD DS Azure pro vašeho tenanta. Z bezpečnostních důvodů Azure AD také neukládá přihlašovací údaje hesla ve formě nešifrovaných textů. Proto služba Azure AD nemůže automaticky generovat tyto hodnoty hash hesla NTLM nebo Kerberos na základě stávajících přihlašovacích údajů uživatelů.

U uživatelských účtů jenom pro Cloud musí uživatelé změnit svoje heslo, aby mohli používat spravovanou doménu. Tento proces změny hesla způsobí, že se ve službě Azure AD vygenerují a ukládají hodnoty hash hesel pro ověřování pomocí protokolu Kerberos a NTLM. Účet není synchronizovaný z Azure AD do Azure služba AD DS, dokud se nezmění heslo.

Pro uživatele synchronizované z místního služba AD DS prostředí pomocí Azure AD Connect [Povolte synchronizaci hodnot hash hesel][hybrid-phs].

> [!IMPORTANT]
> Pokud povolíte Azure služba AD DS pro vašeho tenanta Azure AD, Azure AD Connect synchronizuje jenom starší hodnoty hash hesel. Starší hodnoty hash hesel se nepoužívají, pokud používáte jenom Azure AD Connect k synchronizaci místního prostředí služba AD DS s Azure AD.
>
> Pokud vaše starší aplikace nepoužívají ověřování NTLM nebo jednoduché vazby LDAP, doporučujeme pro Azure služba AD DS zakázat synchronizaci hodnot hash hesel protokolu NTLM. Další informace najdete v tématu [zakázání slabých šifrovacích sad a synchronizace hodnot hash přihlašovacích údajů NTLM][secure-domain].

Po správné konfiguraci budou použitelné hodnoty hash hesel uloženy ve spravované doméně. Pokud odstraníte spravovanou doménu, odstraní se také všechny hodnoty hash hesel uložené v tomto okamžiku. Informace o synchronizovaných přihlašovacích údajích ve službě Azure AD se nedají znovu použít, pokud později vytvoříte jinou spravovanou doménu – je potřeba znovu nakonfigurovat synchronizaci hodnot hash hesel, aby se znovu ukládaly hodnoty hash hesel. Virtuální počítače připojené k doméně nebo uživatelé nebudou moct hned ověřit – Azure AD potřebuje vygenerovat a uložit hodnoty hash hesel v nové spravované doméně. Další informace najdete v tématu [proces synchronizace hodnot hash hesel pro Azure služba AD DS a Azure AD Connect][azure-ad-password-sync].

> [!IMPORTANT]
> Azure AD Connect by měl být nainstalovaný a nakonfigurovaný jenom pro synchronizaci s místními služba AD DS prostředími. Instalace Azure AD Connect ve spravované doméně není podporovaná pro synchronizaci objektů zpět do Azure AD.

## <a name="forests-and-trusts"></a>Doménové struktury a vztahy důvěryhodnosti

*Doménová struktura* je logická konstrukce, kterou používá Active Directory Domain Services (služba AD DS) k seskupení jedné nebo více *domén*. Domény pak uchovávají objekty pro uživatele nebo skupiny a poskytují ověřovací služby.

V Azure služba AD DS doménová struktura obsahuje jenom jednu doménu. Místní doménové struktury služba AD DS často obsahují mnoho domén. Ve velkých organizacích, zejména po fúzích a akvizicích, můžete mít několik místních doménových struktur, které každý z nich obsahuje víc domén.

Ve výchozím nastavení je spravovaná doména vytvořena jako doménová struktura *uživatelů* . Tento typ doménové struktury synchronizuje všechny objekty z Azure AD, včetně všech uživatelských účtů vytvořených v místním služba AD DS prostředí. Uživatelské účty se můžou přímo ověřovat proti spravované doméně, třeba pro přihlášení k virtuálnímu počítači připojenému k doméně. Doménová struktura uživatelů funguje, když je možné synchronizovat hodnoty hash hesla a uživatelé nepoužívají exkluzivní metody přihlašování, jako je ověřování pomocí čipové karty.

V doménové struktuře *prostředků* Azure služba AD DS se uživatelé ověřují pomocí jednosměrné *důvěryhodnosti* doménové struktury ze své místní služba AD DS. S tímto přístupem se uživatelské objekty a hodnoty hash hesel nesynchronizují do Azure služba AD DS. Uživatelské objekty a přihlašovací údaje existují pouze v místních služba AD DS. Tento přístup umožňuje podnikům hostovat prostředky a aplikační platformy v Azure, které jsou závislé na klasických ověřováních, jako jsou protokoly LDAP, Kerberos nebo NTLM, ale všechny problémy s ověřováním nebo obavy se odeberou.

Další informace o typech doménové struktury v Azure služba AD DS najdete v tématu [co jsou doménové struktury prostředků?][concepts-forest] a [jak vztahy důvěryhodnosti doménové struktury fungují v Azure služba AD DS?][concepts-trust]

## <a name="azure-ad-ds-skus"></a>SKU Azure služba AD DS

V Azure služba AD DS jsou dostupné výkony a funkce založené na SKU. SKU vyberete při vytváření spravované domény a po nasazení spravované domény můžete přepínat SKU podle vašich obchodních požadavků. Následující tabulka popisuje dostupné SKU a rozdíly mezi nimi:

| Název SKU   | Maximální počet objektů | Frekvence zálohování | Maximální počet odchozích vztahů důvěryhodnosti doménové struktury |
|------------|----------------------|------------------|----|
| Standard   | Unlimited            | Každých 7 dní     | 0  |
| Enterprise | Unlimited            | Každé 3 dny     | 5  |
| Premium    | Unlimited            | Každý den            | 10 |

Před těmito SKU služby Azure služba AD DS byly použity fakturační modely založené na počtu objektů (uživatelských a počítačových účtů) ve spravované doméně. V závislosti na počtu objektů ve spravované doméně už neexistují variabilní ceny.

Další informace najdete na stránce s [cenami pro Azure služba AD DS][pricing].

### <a name="managed-domain-performance"></a>Výkon spravované domény

Výkon domény se liší v závislosti na tom, jak je ověřování pro aplikaci implementováno. Další výpočetní prostředky mohou pomoci zlepšit dobu odezvy na dotaz a zkrátit dobu trvání operací synchronizace. Při zvýšení úrovně SKU se zvýší i výpočetní prostředky dostupné pro spravovanou doménu. Monitorujte výkon svých aplikací a naplánujte požadované prostředky.

Pokud se vaše společnost nebo aplikace změní a pro spravovanou doménu potřebujete další výpočetní výkon, můžete přejít na jinou SKU.

### <a name="backup-frequency"></a>Frekvence zálohování

Frekvence zálohování určuje, jak často se snímek spravované domény bere. Zálohy jsou automatizovaný proces spravovaný platformou Azure. V případě potíží se spravovanou doménou vám podpora Azure pomůže při obnovení ze zálohy. Protože synchronizace probíhá pouze jednou *ze* služby Azure AD, jakékoli problémy ve spravované doméně nebudou mít vliv na Azure AD nebo místní služba AD DS prostředí a funkce.

Při zvýšení úrovně SKU se zvyšuje frekvence zálohování snímků. Zkontrolujte své obchodní požadavky a cíl bodu obnovení (RPO) a určete požadovanou četnost zálohování pro spravovanou doménu. Pokud se vaše obchodní nebo aplikační požadavky změní a potřebujete častější zálohování, můžete přejít na jinou SKLADOVOU položku.

### <a name="outbound-forest-trusts"></a>Vztahy důvěryhodnosti odchozích doménových struktur

Předchozí část podrobná jednosměrná odchozí důvěryhodnost doménové struktury ze spravované domény do místního prostředí služba AD DS. SKU určuje maximální počet vztahů důvěryhodnosti doménové struktury, které můžete vytvořit pro spravovanou doménu. Zkontrolujte požadavky firmy a aplikace, abyste zjistili, kolik vztahů důvěryhodnosti skutečně potřebujete, a vyberte příslušnou SKLADOVOU položku Azure služba AD DS. Pokud se vaše obchodní požadavky změní a potřebujete vytvořit další vztahy důvěryhodnosti doménové struktury, můžete přejít na jinou SKU.

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

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
