---
title: Porovnání služeb založených na službě Active Directory v Azure | Dokumenty společnosti Microsoft
description: V tomto přehledu porovnáte různé nabídky identit pro služby Active Directory Domain Services, Azure Active Directory a Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: d2495605cccf658b15e812fd85fd65671e84d15b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76544272"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>Porovnání samospravovaných služeb Active Directory Domain Services, Služby Azure Active Directory a spravované služby Azure Active Directory Domain Services

Chcete-li poskytovat aplikacím, službám nebo zařízením přístup k centrální identitě, existují tři běžné způsoby použití služeb založených na službě Active Directory v Azure. Tato volba v řešeníidentit y vám dává flexibilitu používat nejvhodnější adresář pro potřeby vaší organizace. Pokud například většinou spravujete uživatele pouze pro cloud, kteří provozují mobilní zařízení, nemusí mít smysl vytvářet a spouštět vlastní řešení identity služby Active Directory Domain Services (AD DS). Místo toho můžete použít pouze službu Azure Active Directory.

Přestože tři řešení identit založených na službě Active Directory sdílejí běžný název a technologii, jsou navržena tak, aby poskytovala služby, které splňují různé požadavky zákazníků. Na vysoké úrovni jsou tato řešení identit a sady funkcí:

* **Server LDAP (Active Directory Domain Services)** – server LDAP (Enterprise-ready lightweight directory access protocol), který poskytuje klíčové funkce, jako je identita a ověřování, správa objektů počítače, zásady skupiny a vztahy důvěryhodnosti.
    * Služba AD DS je centrální součástí mnoha organizací s místním prostředím IT a poskytuje základní funkce ověřování uživatelských účtů a správy počítače.
* **Azure Active Directory (Azure AD)** – cloudová správa identit a mobilních zařízení, která poskytuje uživatelské účet a ověřovací služby pro prostředky, jako je Office 365, portál Azure nebo aplikace SaaS.
    * Azure AD lze synchronizovat s místním prostředím služby AD DS a poskytnout tak uživatelům, kteří nativně fungují v cloudu, je dinou identitu.
* **Azure Active Directory Domain Services (Azure AD DS)** – poskytuje služby spravované domény s podmnožinou plně kompatibilních tradičních funkcí služby AD DS, jako je připojení k doméně, zásady skupiny, protokol LDAP a ověřování protokolu Kerberos / NTLM.
    * Azure AD DS integruje s Azure AD, který sám můžete synchronizovat s místním prostředím Služby AD DS. Tato možnost rozšiřuje případy použití centrální identity na tradiční webové aplikace, které běží v Azure jako součást strategie výtahu a posunu.

Tento přehled článek porovnává a kontrastuje, jak tato řešení identity může spolupracovat nebo by být použity nezávisle, v závislosti na potřebách vaší organizace.

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Azure AD DS a samoobslužná služba AD DS

Pokud máte aplikace a služby, které potřebují přístup k tradičním mechanismům ověřování, jako je kerberos nebo NTLM, existují dva způsoby poskytování služby Active Directory Domain Services v cloudu:

* *Spravovaná* doména, kterou vytvoříte pomocí služby Azure Active Directory Domain Services (Azure AD DS). Společnost Microsoft vytváří a spravuje požadované prostředky.
* *Samospravovaná* doména, kterou vytvoříte a nakonfigurujete pomocí tradičních prostředků, jako jsou virtuální počítače (VM), hostovaný operační systém Windows Server a služba AD DS (Active Directory Domain Services). Potom pokračovat ve správě těchto prostředků.

S Azure AD DS, základní součásti služby jsou nasazeny a udržovány pro vás Microsoft jako prostředí *spravované* domény. Infrastrukturu služby AD DS nenasazujete, nespravujete a nezabezpečujete pro součásti, jako jsou virtuální počítače, operační systém Windows Server nebo řadiče domény (DC).

Azure AD DS poskytuje menší podmnožinu funkcí tradiční hospo- Například neexistují žádné doménové struktury služby AD, domény, lokality a replikační odkazy na návrh a údržbu. Pro aplikace a služby, které běží v cloudu a potřebují přístup k tradičním mechanismům ověřování, jako je kerberos nebo NTLM, Poskytuje Azure AD DS prostředí spravované domény s minimálním množstvím administrativní režie.

Při nasazování a spouštění samospravovaného prostředí služby AD DS je třeba udržovat všechny přidružené součásti infrastruktury a adresáře. Je tu další nároky na údržbu s prostředím služby AD DS se správou, ale pak můžete provést další úkoly, jako je rozšíření schématu nebo vytvoření vztahů důvěryhodnosti doménové struktury.

Běžné modely nasazení pro samoobslužné prostředí služby AD DS, které poskytuje identitu aplikacím a službám v cloudu, zahrnují následující:

* **Samostatný cloudový systém AD DS –** virtuální počítače Azure jsou nakonfigurované jako řadiče domény a je vytvořeno samostatné prostředí služby AD DS pouze pro cloud. Toto prostředí ad DS se neintegruje s místním prostředím ad ds. K přihlášení a správě virtuálních připojení v cloudu se používá jiná sada přihlašovacích údajů.
* **Nasazení doménové struktury** prostředků – virtuální počítače Azure jsou nakonfigurované jako řadiče domény a vytvoří se doména služby AD DS, která je součástí existující doménové struktury. Vztah důvěryhodnosti je pak nakonfigurován pro místní prostředí ad ds. Ostatní virtuální počítače Azure můžete dodomény připojení k této doménové struktury prostředků v cloudu. Ověřování uživatelů běží přes připojení VPN / ExpressRoute k místnímu prostředí služby AD DS.
* **Rozšíření místní domény do Azure** – virtuální síť Azure se připojuje k místní síti pomocí připojení VPN / ExpressRoute. Virtuální počítače Azure se připojují k této virtuální síti Azure, která jim umožňuje připojit se k místnímu prostředí Služby AD DS.
    * Alternativou je vytvoření virtuálních počítačů Azure a jejich propagace jako řadiče domény repliky z místní domény služby AD DS. Tyto řadiče domény se replikují prostřednictvím připojení VPN / ExpressRoute do místního prostředí služby AD DS. Místní doména služby AD DS se efektivně rozšiřuje do Azure.

Následující tabulka popisuje některé funkce, které může potřebovat pro vaši organizaci, a rozdíly mezi spravovanou doménou Azure AD DS nebo samospravovanou doménou služby AD DS:

| **Funkce** | **Azure AD DS** | **AD DS s vlastním spravem** |
| ----------- |:---------------:|:----------------------:|
| **Spravovaná služba**                               | **&#x2713;** | **&#x2715;** |
| **Bezpečná nasazení**                            | **&#x2713;** | Správce zabezpečuje nasazení |
| **Server DNS**                                    | **&#x2713;** (spravovaná služba) |**&#x2713;** |
| **Oprávnění správce domény nebo rozlehlé sítě** | **&#x2715;** | **&#x2713;** |
| **Připojení k doméně**                                   | **&#x2713;** | **&#x2713;** |
| **Ověřování domény pomocí ntlm a kerberos** | **&#x2713;** | **&#x2713;** |
| **Delegování omezenou protokolem Kerberos**               | Na základě zdrojů | Na základě & založené na účtech založených na prostředcích|
| **Vlastní struktura ou**                           | **&#x2713;** | **&#x2713;** |
| **Zásady skupiny**                                  | **&#x2713;** | **&#x2713;** |
| **Rozšíření schématu**                             | **&#x2715;** | **&#x2713;** |
| **Vztahy důvěryhodnosti domény služby AD nebo doménové struktury**                     | **&#x2713;** (pouze jednosměrné odchozí vztahy důvěryhodnosti doménové struktury) | **&#x2713;** |
| **Zabezpečený LDAP (LDAPS)**                           | **&#x2713;** | **&#x2713;** |
| **Ldap číst**                                     | **&#x2713;** | **&#x2713;** |
| **Ldap zápis**                                    | **&#x2713;** (v rámci spravované domény) | **&#x2713;** |
| **Geograficky distribuovaná nasazení**                   | **&#x2715;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS a Azure AD

Azure AD umožňuje spravovat identitu zařízení používaných organizací a řídit přístup k podnikovým prostředkům z těchto zařízení. Uživatelé mohou také zaregistrovat své osobní zařízení (model přinést vlastní (BYO) s Azure AD, který poskytuje zařízení s identitou. Azure AD pak ověří zařízení, když se uživatel přihlásí k Azure AD a používá zařízení pro přístup k zabezpečeným prostředkům. Zařízení lze spravovat pomocí softwaru MDM (Mobile Device Management), jako je Microsoft Intune. Tato možnost správy umožňuje omezit přístup k citlivým prostředkům na spravovaná zařízení kompatibilní se zásadami.

Tradiční počítače a přenosné počítače se můžou taky připojit ke službě Azure AD. Tento mechanismus nabízí stejné výhody registrace osobního zařízení s Azure AD, například umožnit uživatelům přihlásit se k zařízení pomocí jejich podnikových přihlašovacích údajů.

Připojená zařízení Azure AD vám poskytují následující výhody:

* Jednotné přihlašování (SSO) k aplikacím zabezpečeným službou Azure AD.
* Roaming uživatelských nastavení v souladu s podnikovými zásadami napříč zařízeními.
* Přístup k Windows Storu pro firmy pomocí podnikových přihlašovacích údajů.
* Windows Hello pro firmy.
* Omezený přístup k aplikacím a prostředkům ze zařízení kompatibilních s podnikovými zásadami.

Zařízení se dá připojit k Azure AD s nebo bez hybridního nasazení, které zahrnuje místní prostředí služby AD DS. Následující tabulka popisuje běžné modely vlastnictví zařízení a způsob jejich připojení k doméně:

| **Typ zařízení**                                        | **Platformy zařízení**             | **Mechanismus**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| Osobní zařízení                                          | Windows 10, iOS, Android, Mac OS | Registrováno v Azure AD    |
| Zařízení vlastněné organizací, které není připojeno k místnímu službě AD DS | Windows 10                       | Připojené k Azure AD        |
| Zařízení vlastněné organizací se připojilo k místnímu službě AD DS  | Windows 10                       | k hybridní službě Azure AD. |

Na zařízení s připojeným k Azure AD nebo registrované, ověřování uživatelů se stane pomocí moderních protokolů založených na OAuth / OpenID Connect. Tyto protokoly jsou navrženy tak, aby fungovaly přes internet, takže jsou skvělé pro mobilní scénáře, kde uživatelé přistupují k podnikovým prostředkům odkudkoli.

Se zařízeními připojenými k Azure AD DS můžou aplikace používat protokoly Kerberos a NTLM pro ověřování, takže můžou podporovat starší aplikace migrované ke spuštění na virtuálních počítačích Azure jako součást strategie výtahu a směny. Následující tabulka popisuje rozdíly v tom, jak jsou zařízení reprezentována, a může se ověřit proti adresáři:

| **Aspekt**                      | **Připojeno k Azure AD**                                 | **Azure AD DS-joined**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| Zařízení ovládané            | Azure AD                                            | Spravovaná doména Azure AD DS                                                |
| Reprezentace v adresáři | Objekty zařízení v adresáři Azure AD            | Objekty počítačů ve spravované doméně Azure AD DS                        |
| Ověřování                  | Protokoly založené na OAuth / OpenID Connect              | Protokoly Protokolu Kerberos a NTLM                                               |
| Správa                      | Software pro správu mobilních zařízení (MDM), jako je Intune | Zásady skupiny                                                              |
| Síťové služby                      | Pracuje přes internet                             | Musí být připojen nebo peered s virtuální sítí, kde je nasazená spravovaná doména |
| Skvělé pro...                    | Mobilní nebo stolní zařízení koncových uživatelů                  | Serverové virtuální počítače nasazené v Azure                                              |

## <a name="next-steps"></a>Další kroky

Pokud chcete začít používat Azure AD DS, [vytvořte spravovanou doménu Azure AD DS pomocí webu Azure Portal][tutorial-create].

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
