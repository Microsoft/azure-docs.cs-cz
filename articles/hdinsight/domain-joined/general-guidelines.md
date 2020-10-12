---
title: Obecné pokyny k podnikovému zabezpečení ve službě Azure HDInsight
description: Některé osvědčené postupy, které by měly usnadnit Balíček zabezpečení podniku nasazení a správu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 8d97886232eecc369746e33df484cbfb9d40da72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87530261"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Obecné informace o podnikovém zabezpečení a pokyny v Azure HDInsight

Při nasazení zabezpečeného clusteru HDInsight existují osvědčené postupy, které by měly usnadnit správu nasazení a clusterů. Tady jsou popsány některé obecné informace a pokyny.

## <a name="use-of-secure-cluster"></a>Použití zabezpečeného clusteru

### <a name="recommended"></a>Doporučeno

* Cluster bude používat více uživatelů současně.
* Uživatelé mají různé úrovně přístupu ke stejným datům.

### <a name="not-necessary"></a>Není nutné

* Chystáte se spouštět jenom automatizované úlohy (například jeden uživatelský účet), je standardní cluster dostatečně dobrý.
* Import dat můžete provést pomocí standardního clusteru a použít stejný účet úložiště v jiném zabezpečeném clusteru, kde můžou uživatelé spouštět analytické úlohy.

## <a name="use-of-local-account"></a>Použití místního účtu

* Pokud použijete sdílený uživatelský účet nebo místní účet, bude obtížné zjistit, kdo účet používal ke změně konfigurace nebo služby.
* Používání místních účtů je problematické, pokud uživatelé již nejsou součástí organizace.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>Zásady

* Ve výchozím nastavení používá Ranger jako zásadu **Odepřít** .

* Když je přístup k datům proveden prostřednictvím služby, kde je povolená autorizace:
  * Vyvolal se modul plug-in Ranger Authorization a daný kontext žádosti.
  * Ranger používá zásady nakonfigurované pro službu. Pokud zásady Ranger selžou, je tato kontrolu odložena do systému souborů. Některé služby, jako je MapReduce, kontrolují pouze to, jestli soubor nebo složka patří stejnému uživateli, který odesílá požadavek. Služby, jako je například podregistr, kontrolují buď oprávnění k vlastnictví, nebo příslušná oprávnění systému souborů ( `rwx` ).

* U podregistru, kromě oprávnění k vytváření, aktualizaci a odstraňování oprávnění, by měl mít uživatel `rwx` oprávnění k adresáři v úložišti a všech podadresářích.

* Zásady je možné použít pro skupiny (vhodnější) místo jednotlivců.

* Ranger autorizuje vyhodnotí všechny zásady Ranger pro danou službu pro každý požadavek. Toto vyhodnocení může mít dopad na dobu trvání přijetí úlohy nebo dotazu.

### <a name="storage-access"></a>Přístup k úložišti

* Pokud je typ úložiště WASB, nejedná se o žádný token OAuth.
* Pokud Ranger provedl autorizaci, pak k přístupu k úložišti dojde pomocí spravované identity.
* Pokud Ranger neprovede žádnou autorizaci, dojde k přístupu k úložišti pomocí tokenu OAuth daného uživatele.

### <a name="hierarchical-name-space"></a>Hierarchický obor názvů

Pokud není povolen hierarchický obor názvů:

* Žádná zděděná oprávnění nejsou k dispozici.
* Přiřazovat uživateli přímo v Azure Portal pouze oprávnění systému souborů, která fungují jako **data úložiště xxxx** Azure role.

### <a name="default-hdfs-permissions"></a>Výchozí oprávnění HDFS

* Ve výchozím nastavení uživatelé nemají přístup ke **/** složce v HDFS (k úspěšnému přístupu musí být v roli vlastníka objektu BLOB úložiště).
* Pro pracovní adresář pro MapReduce a další se vytvoří adresář specifický pro uživatele a poskytnou se `sticky _wx` oprávnění. Uživatelé můžou vytvořit soubory a složky pod, ale nemůžou se podívat na jiné položky.

### <a name="url-auth"></a>Ověřování URL

Pokud je povoleno ověřování URL:

* Tato konfigurace bude obsahovat předpony, které jsou zahrnuty v ověřování URL (například `adl://` ).
* Pokud je přístup pro tuto adresu URL, pak Ranger zkontroluje, jestli je uživatel v seznamu povolených.
* Ranger nebude kontrolovat žádné z jemně odstupňovaných zásad.

## <a name="resource-groups"></a>Skupiny prostředků

Pro každý cluster použijte novou skupinu prostředků, abyste mohli rozlišovat mezi prostředky clusteru.

## <a name="nsgs-firewalls-and-internal-gateway"></a>Skupin zabezpečení sítě, brány firewall a interní brány

* Pomocí skupin zabezpečení sítě (skupin zabezpečení sítě) můžete uzamknout virtuální sítě.
* Pomocí brány firewall můžete zpracovávat zásady odchozího přístupu.
* Použijte interní bránu, která není otevřená k veřejnému Internetu.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) je cloudová služba pro správu identit a přístupu od Microsoftu.

### <a name="policies"></a>Zásady

* Zakažte zásady podmíněného přístupu pomocí zásad na základě IP adres. To vyžaduje, aby byly na virtuální sítě, kde jsou nasazené clustery, povoleny koncové body služby. Pokud používáte externí službu pro MFA (jinou než AAD), zásady založené na IP adresách nebudou fungovat.

* `AllowCloudPasswordValidation` pro federované uživatele se vyžadují zásady. Vzhledem k tomu, že HDInsight používá uživatelské jméno/heslo přímo k získání tokenů z Azure AD, musí být tato zásada povolená pro všechny federované uživatele.

* Povolte koncové body služby, pokud požadujete obcházení podmíněného přístupu pomocí důvěryhodných IP adres.

### <a name="groups"></a>Skupiny

* Vždy nasaďte clustery se skupinou.
* Pomocí Azure AD můžete spravovat členství ve skupinách (snadněji než se snažíte spravovat jednotlivé služby v clusteru).

### <a name="user-accounts"></a>Uživatelské účty

* Pro každý scénář použijte jedinečný uživatelský účet. Například použijte účet pro import, použijte jiný pro dotaz nebo jiné úlohy zpracování.
* Místo jednotlivých zásad použijte zásady Ranger založené na skupinách.
* Naplánujte si, jak spravovat uživatele, kteří by už neměli mít přístup k clusterům.

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services

[Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) (Azure služba AD DS) poskytuje spravované doménové služby, jako je připojení k doméně, zásady skupiny, LDAP (Lightweight Directory Access Protocol) a ověřování pomocí protokolu Kerberos/NTLM, které jsou plně kompatibilní se službou Windows Server Active Directory.

Aby se zabezpečené clustery připojily k doméně, vyžaduje se Azure služba AD DS.
HDInsight nemůže záviset na místních řadičích domény ani na vlastních řadičích domény, protože zavádí příliš mnoho chybových bodů, sdílení přihlašovacích údajů, oprávnění DNS atd. Další informace najdete v tématu [Nejčastější dotazy k Azure služba AD DS](../../active-directory-domain-services/faqs.md).

### <a name="azure-ad-ds-instance"></a>Instance Azure služba AD DS

* Vytvořte instanci pomocí `.onmicrosoft.com domain` . Tímto způsobem nebude doména obsluhovat víc serverů DNS.
* Vytvořte certifikát podepsaný svým držitelem pro LDAPs a nahrajte ho do Azure služba AD DS.
* Pro nasazení clusterů použijte partnerský virtuální síť (Pokud máte několik týmů, které nasazují clustery HDInsight ESP, bude to užitečné). Tím zajistíte, že nebudete muset otevírat porty (skupin zabezpečení sítě) ve virtuální síti s řadičem domény.
* Správně nakonfigurujte DNS pro virtuální síť (název domény Azure služba AD DS by se měl vyřešit bez jakýchkoli položek souborů hosta).
* Pokud omezíte odchozí provoz, ujistěte se, že jste si přečetli [podporu brány firewall v HDInsight](../hdinsight-restrict-outbound-traffic.md) .

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Vlastnosti synchronizované z Azure AD do Azure služba AD DS

* Synchronizace služby Azure AD Connect z místního úložiště do Azure AD.
* Služba Azure služba AD DS se synchronizuje z Azure AD.

Azure služba AD DS pravidelně synchronizuje objekty z Azure AD. Okno Azure služba AD DS v Azure Portal zobrazuje stav synchronizace. Během každé fáze synchronizace může dojít ke konfliktu a přejmenování jedinečných vlastností. Věnujte pozornost mapování vlastností z Azure AD do Azure služba AD DS.

Další informace najdete v tématu [populace Azure AD userPrincipalName](../../active-directory/hybrid/plan-connect-userprincipalname.md)a [Jak funguje synchronizace služby Azure služba AD DS](../../active-directory-domain-services/synchronization.md).

### <a name="password-hash-sync"></a>Synchronizace hodnot hash hesel

* Hesla se synchronizují jinak než ostatní typy objektů. V Azure AD a Azure služba AD DS se synchronizují jenom hodnoty hash hesla, které nejsou vratné.
* Místní připojení ke službě Azure AD je nutné povolit prostřednictvím služby AD Connect.
* Služba Azure AD do Azure služba AD DS Sync je automatická (latence jsou za 20 minut).
* Hodnoty hash hesel se synchronizují jenom v případě, že došlo ke změně hesla. Když povolíte synchronizaci hodnot hash hesel, nebudou se všechna stávající hesla automaticky synchronizovat, protože jsou uložená nevratná. Když změníte heslo, hodnoty hash hesel se zobrazí synchronizované.

### <a name="computer-objects-location"></a>Umístění počítačových objektů

Každý cluster je přidružen k jedné organizační jednotce. Interní uživatel se zřídí v organizační jednotce. Všechny uzly jsou připojeny k doméně ve stejné organizační jednotce.

### <a name="active-directory-administrative-tools"></a>Nástroje pro správu služby Active Directory

Postup instalace nástrojů pro správu služby Active Directory na virtuální počítač s Windows serverem najdete v tématu [Instalace nástrojů pro správu](../../active-directory-domain-services/tutorial-create-management-vm.md).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="cluster-creation-fails-repeatedly"></a>Opakované vytvoření clusteru se nezdařilo.

Nejčastější důvody:

* Konfigurace DNS není správná, připojení k doméně uzlů clusteru selže.
* Skupin zabezpečení sítě jsou příliš omezující a znemožňují připojení k doméně.
* Spravovaná identita nemá dostatečná oprávnění.
* Název clusteru není jedinečný pro prvních šest znaků (buď s jiným živým clusterem, nebo s odstraněným clusterem).

## <a name="authentication-setup-and-configuration"></a>Nastavení a konfigurace ověřování

### <a name="user-principal-name-upn"></a>Hlavní název uživatele (UPN)

* Použijte prosím malé písmeno pro všechny služby – hlavní názvy uživatelů (UPN) nerozlišuje velká a malá písmena v clusterech ESP.
* Předpona hlavního názvu uživatele (UPN) by měla odpovídat hodnotě SAMAccountName ve službě Azure AD-DS. Porovnání s polem pošta není vyžadováno.

### <a name="ldap-properties-in-ambari-configuration"></a>Vlastnosti protokolu LDAP v konfiguraci Ambari

Úplný seznam vlastností Ambari, které mají vliv na konfiguraci clusteru HDInsight, najdete v tématu [nastavení ověřování protokolu LDAP v Ambari](https://ambari.apache.org/1.2.1/installing-hadoop-using-ambari/content/ambari-chap2-4.html).

## <a name="next-steps"></a>Další kroky

* [Konfigurace Balíček zabezpečení podniku s využitím Azure Active Directory Domain Services ve službě HDInsight](./apache-domain-joined-configure-using-azure-adds.md)

* [Synchronizace Azure Active Directory uživatelů s clusterem HDInsight](../hdinsight-sync-aad-users-to-cluster.md).
