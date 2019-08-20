---
title: Porovnání služeb založených na službě Active Directory v Azure | Microsoft Docs
description: V tomto přehledu porovnáte různé nabídky identit pro Active Directory Domain Services, Azure Active Directory a Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: e3a8a537ae8c971119cfd08fbf80dc169df1d384
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619303"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>Porovnání samoobslužně spravovaných Active Directory Domain Services, Azure Active Directory a spravovaných Azure Active Directory Domain Services

Pro poskytování aplikací, služeb nebo zařízení přístup k centrální identitě existují tři běžné způsoby použití služeb založených na službě Active Directory v Azure. Tato volba v řešení identity nabízí flexibilitu pro použití nejvhodnějšího adresáře pro potřeby vaší organizace. Pokud například většinou spravujete pouze cloudové uživatele, kteří používají mobilní zařízení, nemusí mít smysl sestavit a spustit vaše vlastní Active Directory Domain Services řešení identity. Místo toho můžete použít Azure Active Directory.

I když tři řešení pro identitu založená na službě Active Directory sdílejí společný název a technologii, jsou navržené tak, aby poskytovaly služby, které splňují různé požadavky zákazníků. Na vysoké úrovni jsou tato řešení identit a sady funkcí:

* **Active Directory Domain Services (služba AD DS)** – server LDAP (Lightweight Directory Access Protocol) připravený pro podnik, který poskytuje klíčové funkce, jako je identita a ověřování, Správa počítačových objektů, zásady skupiny a vztahy důvěryhodnosti.
    * Služba AD DS je centrální komponenta v mnoha organizacích s místním IT prostředím a poskytuje základní funkce pro ověřování a správu počítačů pomocí uživatelského účtu.
* **Azure Active Directory (Azure AD)** – Cloudová identita a Správa mobilních zařízení, které poskytují uživatelský účet a ověřovací služby pro prostředky, jako jsou například Office 365, aplikace Azure Portal nebo SaaS.
    * Azure AD se dá synchronizovat s místním služba AD DSm prostředím a poskytovat tak jedinou identitu uživatelům, kteří pracují nativně v cloudu.
* **Azure Active Directory Domain Services (Azure služba AD DS)** – poskytuje spravované doménové služby s podmnožinou plně kompatibilních tradičních služba AD DS funkcí, jako je připojení k doméně, zásady skupiny, LDAP a ověřování Kerberos/NTLM.
    * Azure služba AD DS se integruje se službou Azure AD, která se může synchronizovat s místním prostředím služba AD DS, aby se v rámci strategie navýšení a přesunutí rozšířily případy použití centrálních identit do tradičních webových aplikací, které běží v Azure.

Tento přehledový článek porovnává a narozdíluje, jak tato řešení identity můžou společně fungovat, nebo se používá nezávisle v závislosti na potřebách vaší organizace.

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Azure služba AD DS a samoobslužně spravované služba AD DS

Pokud máte aplikace a služby, které potřebují přístup k tradičnímu mechanismu ověřování, jako je Kerberos nebo NTLM, existují dva způsoby, jak poskytnout Active Directory Domain Services v cloudu:

* *Spravovaná* doména, kterou vytvoříte pomocí Azure Active Directory Domain Services. Společnost Microsoft vytvoří a spravuje požadované prostředky.
* *Samoobslužná* doména, kterou vytvoříte a konfigurujete pomocí tradičních prostředků, jako jsou virtuální počítače, hostované operační systémy Windows Server a Active Directory Domain Services. Tyto prostředky pak budete dál spravovat.

V případě Azure služba AD DS jsou základní součásti služby nasazeny a udržovány za vás Microsoft jako prostředí *spravované* domény. Nebudete nasazovat, spravovat, opravovat a zabezpečovat infrastrukturu služba AD DS pro součásti, jako jsou virtuální počítače, operační systém Windows Server nebo řadiče domény (DCs). Azure služba AD DS poskytuje menší podmnožinu funkcí tradičního prostředí služba AD DS spravovaného v rámci sebe, což snižuje část složitosti návrhu a správy. Například pro návrh a údržbu nejsou k dispozici žádné doménové struktury služby AD, domény, lokality a replikační odkazy. Pro aplikace a služby, které běží v cloudu a potřebují přístup k tradičním ověřovacím mechanismům, jako je Kerberos nebo NTLM, poskytuje Azure služba AD DS prostředí spravované v doméně s minimálními nároky na správu.

Když nasadíte a spustíte samoobslužně spravované prostředí služba AD DS, je nutné zachovat všechny přidružené infrastruktury a součásti adresáře. Existují další režijní náklady na údržbu pomocí samoobslužného služba AD DSho prostředí, ale můžete provádět další úkoly, jako je například rozšiřování schématu nebo vytvoření vztahů důvěryhodnosti doménové struktury. Mezi běžné modely nasazení pro samoobslužně spravované prostředí služba AD DS, které poskytuje identitu aplikacím a službám v cloudu, patří následující:

* **Samostatné cloudové služba AD DS** – virtuální počítače Azure jsou nakonfigurované jako řadiče domény a vytvoří se samostatné cloudové služba AD DS prostředí. Toto prostředí služba AD DS Neintegruje s místním prostředím služba AD DS. K přihlášení a správě virtuálních počítačů v cloudu se používá jiná sada přihlašovacích údajů.
* **Nasazení doménové struktury prostředků** – virtuální počítače Azure jsou nakonfigurované jako řadiče domény a doména služba AD DS v rámci existující doménové struktury je vytvořená. Vztah důvěryhodnosti je pak nakonfigurovaný na místní služba AD DS prostředí. Jiné virtuální počítače Azure se můžou připojit k této doménové struktuře prostředků v cloudu. Ověřování uživatelů se spouští přes připojení VPN/ExpressRoute k místnímu prostředí služba AD DS.
* **Rozšiřování místní domény do Azure** – virtuální síť Azure se připojuje k místní síti pomocí připojení VPN/ExpressRoute. Virtuální počítače Azure se připojují k této virtuální síti Azure, což jim umožňuje připojení k doméně v místním prostředí služba AD DS.
    * Alternativou je vytvoření virtuálních počítačů Azure a jejich propagace jako repliky řadičů domény z místní domény služba AD DS. Tyto řadiče domény se replikují přes připojení VPN/ExpressRoute do místního prostředí služba AD DS. Místní služba AD DS domény je možné efektivně rozšířit do Azure.

Následující tabulka popisuje některé funkce, které možná budete potřebovat pro vaši organizaci, a rozdíly mezi spravovanou doménou Azure služba AD DS nebo služba AD DS doménou, kterou spravujete samostatně.

| **Funkce** | **služba AD DS Azure** | **služba AD DS samoobslužné správy** |
| ----------- |:---------------:|:----------------------:|
| **Spravovaná služba**                               | **&#x2713;** | **&#x2715;** |
| **Zabezpečená nasazení**                            | **&#x2713;** | Správce zabezpečení nasazení |
| **Server DNS**                                    | **&#x2713;** (spravovaná služba) |**&#x2713;** |
| **Oprávnění správce domény nebo podnikového správce** | **&#x2715;** | **&#x2713;** |
| **Připojení k doméně**                                   | **&#x2713;** | **&#x2713;** |
| **Ověřování domény pomocí protokolu NTLM a protokolu Kerberos** | **&#x2713;** | **&#x2713;** |
| **Omezené delegování protokolu Kerberos**               | Na základě prostředků | Založený na prostředku & na základě účtů|
| **Vlastní struktura organizační jednotky**                           | **&#x2713;** | **&#x2713;** |
| **Zásady skupiny**                                  | **&#x2713;** | **&#x2713;** |
| **Rozšíření schématu**                             | **&#x2715;** | **&#x2713;** |
| **Vztahy důvěryhodnosti domény nebo doménové struktury služby AD**                     | **&#x2715;** | **&#x2713;** |
| **Protokol Secure LDAP (LDAPs)**                           | **&#x2713;** | **&#x2713;** |
| **Čtení protokolu LDAP**                                     | **&#x2713;** | **&#x2713;** |
| **Zápis LDAP**                                    | **&#x2713;** (ve spravované doméně) | **&#x2713;** |
| **Geograficky distribuovaná nasazení**                   | **&#x2715;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure služba AD DS a Azure AD

Azure AD umožňuje spravovat identitu zařízení používaných organizací a řídit přístup k podnikovým prostředkům z těchto zařízení. Uživatelé můžou zaregistrovat svoje osobní zařízení (vlastní nebo vlastní) se službou Azure AD, která poskytuje zařízení identitu. Služba Azure AD pak může ověřit zařízení, když se uživatel přihlásí k Azure AD a použije zařízení k přístupu k zabezpečeným prostředkům. Zařízení je možné spravovat pomocí softwaru pro správu mobilních zařízení (MDM), jako je Microsoft Intune. Tato možnost správy umožňuje omezit přístup k citlivým prostředkům na spravovaná zařízení a zařízení vyhovující zásadám.

K Azure AD se můžou připojit i tradiční počítače a přenosné počítače. Tento mechanismus nabízí stejné výhody registrace osobního zařízení ve službě Azure AD, například k tomu, aby se uživatelé mohli přihlásit k zařízení pomocí svých podnikových přihlašovacích údajů. Zařízení připojená k Azure AD poskytují následující výhody:

* Jednotné přihlašování (SSO) k aplikacím zabezpečeným službou Azure AD.
* Roaming uživatelských nastavení odpovídajících podnikovým zásadám napříč zařízeními.
* Přístup k Windows Storu pro firmy pomocí podnikových přihlašovacích údajů
* Windows Hello pro firmy.
* Omezený přístup k aplikacím a prostředkům ze zařízení, která jsou v souladu s podnikovými zásadami.

Zařízení je možné připojit k Azure AD s hybridním nasazením nebo bez něj, které zahrnuje místní služba AD DS prostředí. Následující tabulka popisuje běžné modely vlastnictví zařízení a to, jak by se obvykle připojily k doméně:

| **Typ zařízení**                                        | **Platformy zařízení**             | **Mechanismy**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| Osobní zařízení                                          | Windows 10, iOS, Android, Mac OS | Registrace Azure AD    |
| Zařízení vlastněné organizací nepřipojená k místnímu služba AD DS | Windows 10                       | Připojeno k Azure AD        |
| Zařízení vlastněné organizací připojené k místnímu služba AD DS  | Windows 10                       | Připojeno k hybridní službě Azure AD |

V případě zařízení připojeného k Azure AD se k ověřování uživatelů používá moderní protokoly založené na protokolu OAuth/OpenID Connect. Tyto protokoly jsou navržené tak, aby fungovaly přes Internet, takže jsou skvělé pro mobilní scénáře, kdy uživatelé přistupují k podnikovým prostředkům odkudkoli. U zařízení připojených k Azure služba AD DS můžou aplikace používat protokoly Kerberos a NTLM pro ověřování, takže v rámci strategie navýšení a posunutí můžou podporovat starší verze aplikací, které se migrují na spouštění na virtuálních počítačích Azure. V následující tabulce jsou uvedeny rozdíly v tom, jak se zařízení reprezentují a můžou se ověřovat proti adresáři:

| **Součástí**                      | **Připojeno k Azure AD**                                 | **Azure služba AD DS – připojeno**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| Zařízení, které ovládá            | Azure AD                                            | Spravovaná doména Azure služba AD DS                                                |
| Reprezentace v adresáři | Objekty zařízení v adresáři služby Azure AD            | Objekty počítače ve spravované doméně Azure služba AD DS                        |
| Ověřování                  | Protokoly založené na protokolu OAuth/OpenID Connect              | Protokoly Kerberos a NTLM                                               |
| Správa                      | Software pro správu mobilních zařízení (MDM), jako je Intune | Zásada skupiny                                                              |
| Sítě                      | Funguje přes Internet                             | Vyžaduje, aby počítače byly ve stejné virtuální síti jako spravovaná doména. |
| Skvělé pro...                    | Mobilní zařízení koncových uživatelů nebo stolních počítačů                  | Serverové virtuální počítače nasazené v Azure                                              |

## <a name="next-steps"></a>Další postup

Pokud chcete začít používat Azure služba AD DS, [vytvořte pomocí Azure Portal spravované domény azure služba AD DS][tutorial-create].

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md