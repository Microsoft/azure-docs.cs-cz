---
title: Obecné pokyny pro podnikové zabezpečení v Azure HDInsight
description: Některé osvědčené postupy, které by měly usnadnit nasazení a správu balíčků enterprise security.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: be6c1fdc5deb6d541656c198469822dae0a5f7c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463203"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Obecné informace a pokyny pro podnikové zabezpečení v Azure HDInsight

Při nasazování zabezpečeného clusteru HDInsight existují některé osvědčené postupy, které by měly usnadnit nasazení a správu clusteru. Některé obecné informace a pokyny jsou popsány zde.

## <a name="use-of-secure-cluster"></a>Použití zabezpečeného clusteru

### <a name="recommended"></a>Doporučené

* Cluster bude používat více uživatelů současně.
* Uživatelé mají různé úrovně přístupu ke stejným datům.

### <a name="not-necessary"></a>Není nutné

* Budete spouštět pouze automatizované úlohy (jako je jeden uživatelský účet), standardní cluster je dost dobrý.
* Import dat můžete provést pomocí standardního clusteru a použít stejný účet úložiště v jiném zabezpečeném clusteru, kde mohou uživatelé spouštět analytické úlohy.

## <a name="use-of-local-account"></a>Použití místního účtu

* Pokud používáte sdílený uživatelský účet nebo místní účet, bude obtížné určit, kdo účet použil ke změně konfigurace nebo služby.
* Použití místních účtů je problematické, když uživatelé již nejsou součástí organizace.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>Zásady

* Ve výchozím nastavení ranger používá **Deny** jako zásadu.

* Při přístupu k datům prostřednictvím služby, kde je povolena autorizace:
  * Modul plug-in autorizace rangeru je vyvolán a vzhledem k kontextu požadavku.
  * Ranger použije zásady nakonfigurované pro službu. Pokud zásady ranger nezdaří, kontrola přístupu je odložena do systému souborů. Některé služby, jako je MapReduce, pouze kontrolují, zda je soubor / složka vlastněna stejným uživatelem, který odesílá žádost. Služby jako Hive, zkontrolujte buď vlastnickou shodu nebo příslušná oprávnění souborového systému (`rwx`).

* Pro Hive, kromě oprávnění k tomu vytvořit / aktualizovat / odstranit `rwx`oprávnění, uživatel by měl mít oprávnění k adresáři na úložišti a všechny podadresáře.

* Zásady lze použít na skupiny (vhodnější) namísto jednotlivců.

* Ranger autorizátor vyhodnotí všechny zásady Ranger pro tuto službu pro každou žádost. Toto hodnocení může mít vliv na čas trvá přijmout úlohu nebo dotaz.

### <a name="storage-access"></a>Přístup k úložišti

* Pokud typ úložiště je WASB, pak žádný token OAuth je zapojen.
* Pokud Ranger provedl autorizaci, pak přístup k úložišti se stane pomocí spravované identity.
* Pokud Ranger neprovedl žádnou autorizaci, pak přístup k úložišti se stane pomocí tokenu OAuth uživatele.

### <a name="hierarchical-name-space"></a>Hierarchický obor názvů

Pokud není povolen hierarchický obor názvů:

* Neexistují žádná zděděná oprávnění.
* Jenom oprávnění souborového **systému,** který funguje, je role Storage Data XXXX RBAC, která se přiřazuje uživateli přímo na webu Azure Portal.

### <a name="default-hdfs-permissions"></a>Výchozí oprávnění HDFS

* Ve výchozím nastavení uživatelé nemají **/** přístup ke složce na HDFS (musí být v roli vlastníka objektu blob úložiště pro přístup k úspěchu).
* Pro pracovní adresář pro mapreduce a ostatní, je vytvořen `sticky _wx` adresář specifický pro uživatele a za předpokladu, oprávnění. Uživatelé mohou vytvářet soubory a složky pod ním, ale nemohou se podívat na jiné položky.

### <a name="url-auth"></a>Auth adresy URL

Pokud je auth url povolena:

* Config bude obsahovat, jaké předpony jsou zahrnuty `adl://`v url auth (jako ).
* Pokud je přístup pro tuto adresu URL, pak Ranger zkontroluje, zda je uživatel v seznamu povolených.
* Ranger nekontroluje žádnou z jemných zrnitých zásad.

## <a name="resource-groups"></a>Skupiny prostředků

Pro každý cluster použijte novou skupinu prostředků, abyste mohli rozlišovat mezi prostředky clusteru.

## <a name="nsgs-firewalls-and-internal-gateway"></a>Sítě nsg, brány firewall a interní brána

* K uzamčení virtuálních sítí použijte skupiny zabezpečení sítě (NSG).
* Ke zpracování zásad odchozího přístupu použijte bránu firewall.
* Použijte interní bránu, která není přístupná veřejnému internetu.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) je cloudová služba správy identit a přístupu společnosti Microsoft.

### <a name="policies"></a>Zásady

* Zakažte zásady podmíněného přístupu pomocí zásad y založené na ip adresách. To vyžaduje, aby koncové body služby byly povoleny na virtuálních počítačích, kde jsou nasazeny clustery. Pokud používáte externí službu pro vícefaktorové řešení (něco jiného než AAD), zásady založené na IP adrese nebudou fungovat

* `AllowCloudPasswordValidation`pro federované uživatele vyžadována zásada. Vzhledem k tomu, že HDInsight používá uživatelské jméno / heslo přímo k získání tokenů z Azure AD, tato zásada musí být povolena pro všechny federované uživatele.

* Pokud požadujete obejití podmíněného přístupu pomocí důvěryhodných IP služeb, povolte koncové body služby.

### <a name="groups"></a>Skupiny

* Vždy nasazujte clustery se skupinou.
* Azure AD slouží ke správě členství ve skupinách (jednodušší než se pokoušet spravovat jednotlivé služby v clusteru).

### <a name="user-accounts"></a>Uživatelské účty

* Pro každý scénář použijte jedinečný uživatelský účet. Můžete například použít účet pro import, použít jiný pro dotaz nebo jiné úlohy zpracování.
* Místo jednotlivých zásad používejte zásady rangerů založené na skupinách.
* Máte plán, jak spravovat uživatele, kteří už nemají přístup ke clusterům.

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Služba Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) poskytuje služby spravované domény, jako je připojení k doméně, zásady skupiny, protokol LDAP (LDAP) a ověřování protokolem Kerberos / NTLM, které je plně kompatibilní se službou Windows Server Active Directory.

Azure AD DS je vyžadováno pro zabezpečené clustery pro připojení k doméně.
HDInsight nemůže záviset na místních řadičích domény nebo vlastních řadičích domény, protože zavádí příliš mnoho bodů selhání, sdílení přihlašovacích údajů, oprávnění DNS a tak dále. Další informace najdete v [tématu Nejčastější dotazy k Azure AD DS](../../active-directory-domain-services/faqs.md).

### <a name="azure-ad-ds-instance"></a>Instance Azure AD DS

* Vytvořte instanci pomocí `.onmicrosoft.com domain`. Tímto způsobem nebude existovat více DNS serverů, které slouží doméně.
* Vytvořte certifikát podepsaný svým držitelem pro LDAPS a nahrajte ho do služby Azure AD DS.
* Použijte virtuální síť s partnerským partnerem pro nasazení clusterů (pokud máte několik týmů nasazujících clustery HDInsight ESP, bude to užitečné). Tím zajistíte, že nebudete muset otevírat porty (NSG) ve virtuální síti s řadičem domény.
* Nakonfigurujte DNS pro virtuální síť správně (název domény Azure AD DS by měl přeložit bez jakýchkoli položek souborů hosts).
* Pokud omezujete odchozí provoz, ujistěte se, že jste si přečetli [podporu brány firewall ve službě HDInsight.](../hdinsight-restrict-outbound-traffic.md)

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Vlastnosti synchronizované z Azure AD do Azure AD DS

* Azure AD připojit synchronizuje z místního Azure AD.
* Azure AD DS synchronizuje z Azure AD.

Azure AD DS synchronizuje objekty z Azure AD pravidelně. Okno Azure AD DS na webu Azure Portal zobrazuje stav synchronizace. Během každé fáze synchronizace se jedinečné vlastnosti mohou dostat do konfliktu a přejmenovány. Věnujte pozornost mapování vlastností z Azure AD na Azure AD DS.

Další informace naleznete v [tématu Naplnění populace Azure AD UserPrincipalName](../../active-directory/hybrid/plan-connect-userprincipalname.md)a [Jak funguje synchronizace Azure AD DS](../../active-directory-domain-services/synchronization.md).

### <a name="password-hash-sync"></a>Synchronizace hash hesla

* Hesla jsou synchronizována odlišně od jiných typů objektů. Ve službě Azure AD a Azure AD DS se synchronizují jenom nevratné hash hesel.
* Místní azure ad musí být povolené prostřednictvím služby AD Connect
* Synchronizace Azure AD to Azure AD DS je automatická (latence jsou pod 20 minut).
* Změny hashe hesla jsou synchronizovány pouze v případě, že došlo ke změně hesla. Pokud povolíte synchronizaci hash hesel, všechna existující hesla se nesynchronizují automaticky, protože jsou nevratně uložena. Při změně hesla se synchronizují změny hashe hesel.

### <a name="computer-objects-location"></a>Umístění počítačových objektů

Každý cluster je přidružen k jedné řadové výmětové opouře. Interní uživatel je zřízen a v hlavní výužně. Všechny uzly jsou doménou spojenou do stejné ou.

### <a name="active-directory-administrative-tools"></a>Nástroje pro správu služby Active Directory

Postup instalace nástrojů pro správu služby Active Directory na virtuální počítač se systémem Windows Server naleznete v [tématu Instalace nástrojů pro správu](../../active-directory-domain-services/tutorial-create-management-vm.md).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="cluster-creation-fails-repeatedly"></a>Vytváření clusteru se nezdaří opakovaně

Nejčastější důvody:

* Konfigurace DNS není správná, připojení domény uzlů clusteru se nezdaří.
* Skupiny nsg jsou příliš omezující a brání připojení k doméně.
* Spravovaná identita nemá dostatečná oprávnění.
* Název clusteru není jedinečný na prvních šesti znacích (buď s jiným živým clusterem, nebo s odstraněným clusterem).

## <a name="next-steps"></a>Další kroky

* [Konfigurace balíčků podnikového zabezpečení se službou Azure Active Directory Domain Services v HDInsightu](./apache-domain-joined-configure-using-azure-adds.md)

* [Synchronizujte uživatele služby Azure Active Directory s clusterem HDInsight](../hdinsight-sync-aad-users-to-cluster.md).
