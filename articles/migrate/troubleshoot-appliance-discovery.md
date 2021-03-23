---
title: Řešení potíží s nasazením a zjišťováním Azure Migrate zařízení
description: Získejte pomoc s nasazením zařízení a zjišťováním serveru.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: c952fe33b434aac972be6a1eb03b63698eb64fc6
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782312"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Řešení potíží s Azure Migrate zařízením a zjišťováním

Tento článek vám pomůže při řešení potíží při nasazování zařízení [Azure Migrate](migrate-services-overview.md) a používání zařízení ke zjišťování místních serverů.

## <a name="whats-supported"></a>Co je podporováno?

[Zkontrolujte](migrate-appliance.md) požadavky na podporu zařízení.

## <a name="invalid-ovf-manifest-entry"></a>Neplatný záznam manifestu OVF

Pokud se zobrazí chyba "zadaný soubor manifestu je neplatný: Neplatná položka manifestu OVF", udělejte toto:

1. Zkontrolujte jeho hodnotu hash tak, že zkontrolujete, jestli se soubor vajíček Azure Migrate zařízení správně stáhl. [Další informace](./tutorial-discover-vmware.md). Pokud hodnota hash neodpovídá, Stáhněte si znovu soubor vajíček a spusťte nasazení znovu.
2. Pokud se nasazení stále nedaří a k nasazení souboru OVF používáte klienta VMware vSphere, zkuste ho nasadit prostřednictvím webového klienta vSphere. Pokud nasazení ještě neproběhne úspěšně, zkuste použít jiný webový prohlížeč.
3. Pokud používáte webového klienta vSphere a pokusíte se ho nasadit na vCenter Server 6,5 nebo 6,7, zkuste nasadit VAJÍČKu přímo na hostiteli ESXi:
   - Připojte se k hostiteli ESXi přímo (místo vCenter Server) s webovým klientem ( *IP adresa hostitele* https://<>/UI).
   - V **domovském**  >  **inventáři** vyberte **soubor**  >  **nasadit šablonu OVF**. Přejděte k VAJÍČKám a dokončete nasazení.
4. Pokud se nasazení stále nedaří, obraťte se na podporu Azure Migrate.

## <a name="cant-connect-to-the-internet"></a>Nejde se připojit k Internetu.

K tomu může dojít, pokud je server zařízení za proxy serverem.

- Pokud proxy server vyžaduje přihlašovací údaje pro autorizaci, zkontrolujte, že jste je zadali.
- Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, přidejte [tyto adresy URL](migrate-appliance.md#url-access) do povolených.
- Pokud pro připojení k Internetu používáte zachycení proxy serveru, importujte certifikát proxy serveru do zařízení pomocí [tohoto postupu](./migrate-appliance.md).

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Z webové aplikace zařízení se nejde přihlásit k Azure.

Pokud k přihlášení do Azure používáte nesprávný účet Azure, zobrazí se chyba "je nám líto, ale došlo k potížím s přihlášením". K této chybě dochází z několika důvodů:

- Pokud se přihlásíte k webové aplikaci zařízení pro veřejný cloud, použijte přihlašovací údaje uživatelského účtu pro cloudový portál pro státní správu.
- Pokud se přihlásíte k webové aplikaci zařízení pro oficiální Cloud pomocí přihlašovacích údajů uživatelského účtu pro portál privátního cloudu.

Ujistěte se, že používáte správné přihlašovací údaje.

## <a name="datetime-synchronization-error"></a>Chyba synchronizace data a času

Chyba synchronizace data a času (802) znamená, že hodiny serveru pravděpodobně nejsou synchronizovány s aktuálním časem více než pěti minutami. Změňte čas, který bude na serveru sběrače odpovídat aktuálnímu času:

1. Otevřete příkazový řádek správce na serveru.
2. Chcete-li kontrolovat časové pásmo, spusťte příkaz **W32tm/TZ**.
3. Chcete-li synchronizovat čas, spusťte příkaz **W32tm/Resync**.

## <a name="unabletoconnecttoserver"></a>"UnableToConnectToServer"

Pokud se zobrazí tato chyba připojení, možná se nebudete moci připojit k vCenter Server *servername*. com: 9443. Podrobnosti o chybě označují, že se na něj nenaslouchá žádný koncový bod `https://\*servername*.com:9443/sdk` , který by mohl zprávu přijmout.

- Ověřte, zda používáte nejnovější verzi zařízení. Pokud nejste, upgradujte zařízení na [nejnovější verzi](./migrate-appliance.md).
- Pokud problém pořád proběhne v nejnovější verzi, zařízení nemusí být schopné přeložit zadaný vCenter Server název, nebo je možné, že je zadaný port nesprávný. Ve výchozím nastavení se kolektor pokusí připojit k portu číslo 443, pokud není zadaný port.

    1. Ověřte z zařízení parametr *servername*. com.
    2. Pokud se krok 1 nezdařil, zkuste se připojit k serveru vCenter pomocí IP adresy.
    3. Identifikujte správné číslo portu pro připojení k vCenter Server.
    4. Ověřte, že je vCenter Server v provozu.

## <a name="error-6005260039-appliance-might-not-be-registered"></a>Chyba 60052/60039: zařízení pravděpodobně není zaregistrováno.

- Chyba 60052: Pokud účet Azure, který se používá k registraci zařízení, nemá dostatečná oprávnění, "zařízení se nemusí úspěšně zaregistrovat do projektu".
    - Ujistěte se, že uživatelský účet Azure použitý k registraci zařízení má alespoň oprávnění přispěvatele k tomuto předplatnému.
    - [Přečtěte si další informace](./migrate-appliance.md#appliance---vmware) o požadovaných rolích a oprávněních Azure.
- Chyba 60039, "zařízení se nemusí úspěšně zaregistrovat do projektu" může dojít, pokud se registrace nezdaří, protože se nepovedlo najít projekt použitý k registraci zařízení.
    - V Azure Portal a ověřte, zda projekt existuje ve skupině prostředků.
    - Pokud projekt neexistuje, vytvořte ve své skupině prostředků nový projekt a znovu zaregistrujte zařízení. [Přečtěte si, jak](./create-manage-projects.md#create-a-project-for-the-first-time) vytvořit nový projekt.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Chyba 60030/60031: operace správy Key Vault nebyla úspěšná.

Pokud se zobrazí chyba 60030 nebo 60031, "operace správy Azure Key Vault nebyla úspěšná", udělejte toto:

- Ujistěte se, že uživatelský účet Azure použitý k registraci zařízení má alespoň oprávnění přispěvatele k tomuto předplatnému.
- Ujistěte se, že má účet přístup k trezoru klíčů, který je zadaný v chybové zprávě, a pak zkuste operaci zopakovat.
- Pokud potíže trvají, obraťte se na podporu Microsoftu.
- [Přečtěte si další informace](./migrate-appliance.md#appliance---vmware) o požadovaných rolích a oprávněních Azure.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Chyba 60028: zjišťování se nepovedlo inicializovat.

Chyba 60028: zjišťování nelze iniciovat z důvodu chyby. Operace se pro zadaný seznam hostitelů nebo clusterů nezdařila. znamená to, že zjišťování nebylo možné spustit na hostitelích uvedených v této chybě z důvodu problému s přístupem nebo načítáním informací o serveru. Zbytek hostitelů byl úspěšně přidán.

- Přidejte hostitele uvedené v této chybě znovu pomocí možnosti **Přidat hostitele** .
- Pokud dojde k chybě ověřování, přečtěte si pokyny k nápravě a opravte chyby a potom zkuste znovu **spustit možnost Uložit a spustit zjišťování** .

## <a name="error-60025-azure-ad-operation-failed"></a>Chyba 60025: operace Azure AD selhala

Chyba 60025: operace Azure AD se nezdařila. K chybě při vytváření nebo aktualizaci aplikace Azure AD dojde v případě, že se uživatelský účet Azure, který se používá k zahájení zjišťování, liší od účtu použitého k registraci zařízení. Proveďte některou z následujících akcí:

- Ujistěte se, že uživatelský účet, který spouští zjišťování, je stejný jako ten, který se používá k registraci zařízení.
- Zadejte Azure Active Directory oprávnění k přístupu k aplikacím pro uživatelský účet, pro který se operace zjišťování nedaří.
- Odstraňte skupinu prostředků vytvořenou dříve pro projekt. Vytvořte novou skupinu prostředků a začněte znovu.
- [Přečtěte si další informace](./migrate-appliance.md#appliance---vmware) o Azure Active Directory oprávnění aplikace.

## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Chyba 50004: Nelze se připojit k hostiteli nebo clusteru

Chyba 50004: Nelze se připojit k hostiteli nebo clusteru, protože název serveru nelze přeložit. Kód chyby WinRM: 0x803381B9 se může vyskytnout, pokud služba Azure DNS pro zařízení nemůže vyřešit zadaný název clusteru nebo hostitele.

- Pokud se tato chyba zobrazuje v clusteru, plně kvalifikovaný název domény clusteru.
- Tato chyba se může zobrazit také pro hostitele v clusteru. To znamená, že zařízení se může připojit ke clusteru, ale cluster vrátí názvy hostitelů, které nejsou plně kvalifikované názvy domén. Chcete-li tuto chybu vyřešit, aktualizujte soubor hostitelů na zařízení přidáním mapování IP adresy a názvů hostitelů:
    1. Otevřete Poznámkový blok jako správce.
    2. Otevřete soubor C:\Windows\System32\Drivers\etc\hosts.
    3. Přidejte IP adresu a název hostitele do řádku. Opakujte pro každého hostitele nebo cluster, kde se zobrazí tato chyba.
    4. Uložte a zavřete soubor hostitelů.
    5. Ověřte, jestli se zařízení může připojit k hostitelům pomocí aplikace pro správu zařízení. Po 30 minutách byste měli vidět nejnovější informace pro tyto hostitele v Azure Portal.

## <a name="error-60001-unable-to-connect-to-server"></a>Chyba 60001: Nelze se připojit k serveru

- Zajistěte, aby ze zařízení bylo připojení k serveru.
- Pokud se jedná o server Linux, zajistěte, aby ověřování na základě hesla bylo povoleno pomocí následujících kroků:
    1. Přihlaste se k serveru Linux a otevřete konfigurační soubor SSH pomocí příkazu "VI/etc/ssh/sshd_config".
    2. Nastavte možnost PasswordAuthentication na Ano. Soubor uložte.
    3. Restartujte službu SSH spuštěním příkazu Service sshd restart.
- Pokud se jedná o Windows Server, zajistěte, aby byl port 5985 otevřený pro vzdálenou volání rozhraní WMI.
- Pokud zjišťujete Server GCP Linux a použijete uživatele root, změňte výchozí nastavení pro kořenové přihlášení pomocí následujících příkazů.
    1. Přihlaste se k serveru Linux a otevřete konfigurační soubor SSH pomocí příkazu "VI/etc/ssh/sshd_config".
    2. Nastavte možnost PermitRootLogin na Ano.
    3. Restartujte službu SSH spuštěním příkazu Service sshd restart.

## <a name="error-no-suitable-authentication-method-found"></a>Chyba: nebyla nalezena žádná vhodná metoda ověřování.

Zajistěte, aby na serveru se systémem Linux bylo povoleno ověřování na základě hesla, a to pomocí následujících kroků:
    1. Přihlaste se k serveru Linux a otevřete konfigurační soubor SSH pomocí příkazu "VI/etc/ssh/sshd_config".
    2. Nastavte možnost PasswordAuthentication na Ano. Soubor uložte.
    3. Restartujte službu SSH spuštěním příkazu Service sshd restart.

## <a name="discovered-servers-not-in-portal"></a>Zjištěné servery, které nejsou na portálu

Pokud je stav zjišťování "Probíhá zjišťování", ale servery ještě nejsou na portálu zobrazeny, počkejte několik minut:

- Server na VMware trvá přibližně 15 minut.
- Každý přidaný hostitel pro servery na zjišťování technologie Hyper-V trvá přibližně dvě minuty.

Pokud počkáte a stav se nezmění, vyberte **aktualizovat** na kartě **servery** . Mělo by se zobrazit počet zjištěných serverů ve Azure Migrate: zjišťování a vyhodnocení a Azure Migrate: Migrace serveru.

Pokud to nefunguje a chystáte se zjišťovat servery VMware:

- Ověřte, že zadaný účet vCenter má správně nastavená oprávnění, a to s přístupem k aspoň jednomu serveru.
- Azure Migrate nemůže vyhledat servery na VMware, pokud má účet vCenter udělen přístup na úrovni složky virtuálního počítače vCenter. [Přečtěte si další informace](set-discovery-scope.md) o zjišťování rozsahu.

## <a name="server-data-not-in-portal"></a>Data serveru nejsou na portálu

Pokud se zjištěné servery nezobrazí na portálu nebo pokud jsou data serveru zastaralá, počkejte několik minut. Na portálu se zobrazí změny zjištěných dat konfigurace serveru až po dobu 30 minut. Může trvat několik hodin, než se změny v datech inventáře softwaru zobrazí. Pokud po této době žádná data neexistují, zkuste aktualizovat následujícím způsobem.

1. V **systémech Windows, Linux a SQL Server**  >  **Azure Migrate: zjišťování a posouzení** vyberte **Přehled**.
2. V části **Spravovat** vyberte **Agent Health**.
3. Vyberte **aktualizovat agenta**.
4. Počkejte na dokončení operace aktualizace. Nyní byste měli vidět aktuální informace.

## <a name="deleted-servers-appear-in-portal"></a>Odstraněné servery se zobrazí na portálu.

Pokud odstraníte servery, které se pořád zobrazují na portálu, počkejte 30 minut. Pokud se pořád zobrazují, aktualizujte je tak, jak je popsáno výše.

## <a name="discovered-software-inventory-and-sql-server-instances-and-databases-not-in-portal"></a>Zjištěný inventář softwaru a instance SQL Server a databáze, které nejsou na portálu

Po zahájení zjišťování na zařízení může trvat až 24 hodin, než se data inventáře na portálu začnou zobrazovat.

Pokud jste v nástroji Configuration Manager pro zařízení nezadali přihlašovací údaje pro ověřování systému Windows nebo SQL Server pověření, přidejte přihlašovací údaje, aby je zařízení mohl používat k připojení k příslušným SQL Server instancí.

Po připojení zařízení shromáždí údaje o konfiguraci a výkonu SQL Server instance a databáze. Konfigurační data SQL Server se aktualizují jednou za 24 hodin a data o výkonu se zaznamenávají každých 30 sekund. Proto jakékoli změny vlastností instance SQL Server a databází, jako je stav databáze, úroveň kompatibility atd. může trvat až 24 hodin, než se aktualizuje na portálu.

## <a name="sql-server-instance-is-showing-up-in-not-connected-state-on-portal"></a>Na portálu se zobrazuje stav Nepřipojeno SQL Server instance.

Pokud chcete zobrazit problémy zjištěné během zjišťování SQL Server instancí a databází, klikněte na stránce zjištěné servery v projektu na stav Nepřipojeno ve sloupci Stav připojení.

Vytváření posouzení na serverech, které obsahují instance SQL, které se zcela nezjistily nebo jsou v nepřipojeném stavu, může vést k připravenosti označenou jako "neznámá".

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Nezobrazují se údaje o výkonu pro některé síťové adaptéry na mých fyzických serverech.

K tomu může dojít, pokud má fyzický server zapnutou virtualizaci Hyper-V. Kvůli přípravku je propustnost sítě zachycena na zjištěných virtuálních síťových adaptérech.

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>Chyba: Nahraný soubor není v očekávaném formátu

Některé nástroje mají místní nastavení, které zajistí vytvoření souboru CSV se středníkem jako oddělovačem. Změňte toto nastavení tak, aby oddělovačem byla čárka.

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>Po importu souboru CSV se zobrazuje zpráva Probíhá zjišťování

Tento stav se zobrazí, pokud selhalo nahrávání sdíleného svazku clusteru z důvodu chyby ověření. Zkuste soubor CSV importovat znovu. Můžete si stáhnout sestavu chyb předchozího nahrávání a podle doporučených pokynů v tomto souboru opravit chyby. Zprávu o chybách si můžete stáhnout z části Import podrobností na stránce vyhledat servery.

## <a name="do-not-see-software-inventory-details-even-after-updating-guest-credentials"></a>Nezobrazuje podrobnosti inventáře softwaru ani po aktualizaci přihlašovacích údajů hostů.

Zjišťování inventáře softwaru se spouští jednou za 24 hodin. Chcete-li zobrazit podrobnosti okamžitě, proveďte aktualizaci následujícím způsobem. To může trvat několik minut v závislosti na ne. zjištěných serverů.

1. V **systémech Windows, Linux a SQL Server**  >  **Azure Migrate: zjišťování a posouzení** vyberte **Přehled**.
2. V části **Spravovat** vyberte **Agent Health**.
3. Vyberte **aktualizovat agenta**.
4. Počkejte na dokončení operace aktualizace. Nyní byste měli vidět aktuální informace.

## <a name="unable-to-export-software-inventory"></a>Inventář softwaru nelze exportovat.

Zajistěte, aby uživatel stáhl inventář z portálu má oprávnění přispěvatele k tomuto předplatnému.

## <a name="no-suitable-authentication-method-found-to-complete-authentication-publickey"></a>Nebyla nalezena žádná vhodná metoda ověřování pro dokončení ověřování (PublicKey).

Ověřování založené na klíčích nebude fungovat, použijte ověřování heslem.

## <a name="common-app-discovery-errors"></a>Běžné chyby zjišťování aplikací

Azure Migrate podporuje zjišťování inventáře softwaru pomocí Azure Migrate: zjišťování a hodnocení. Zjišťování aplikací se v současné době podporuje jenom pro VMware. [Přečtěte si další informace](how-to-discover-applications.md) o požadavcích a krocích pro nastavení zjišťování aplikací.

Typické chyby zjišťování aplikací jsou shrnuté v tabulce.

| **Chyba** | **Příčina** | **Akce** |
|--|--|--|
| 9000: nelze zjistit stav nástroje VMware. | Nástroje VMware pravděpodobně nejsou nainstalovány nebo jsou poškozeny. | Zajistěte, aby byly na serveru nainstalované a spuštěné nástroje VMware. |
| 9001: nástroje VMware nejsou nainstalovány. | Nástroje VMware pravděpodobně nejsou nainstalovány nebo jsou poškozeny. | Zajistěte, aby byly na serveru nainstalované a spuštěné nástroje VMware. |
| 9002: nástroje VMware nejsou spuštěny. | Nástroje VMware pravděpodobně nejsou nainstalovány nebo jsou poškozeny. | Zajistěte, aby byly na serveru nainstalované a spuštěné nástroje VMware. |
| 9003: typ operačního systému se pro zjišťování hostovaného serveru nepodporuje. | Operační systém spuštěný na serveru není ani Windows ani Linux. | Podporované typy operačních systémů jsou pouze systémy Windows a Linux. Pokud je server ve skutečnosti Windows nebo Linux, ověřte typ operačního systému zadaný v vCenter Server. |
| 9004: Server není spuštěn. | Server je vypnutý. | Zajistěte, aby byl server zapnutý. |
| 9005: typ operačního systému se pro zjišťování hostovaného serveru nepodporuje. | Typ operačního systému se pro zjišťování hostovaného serveru nepodporuje. | Podporované typy operačních systémů jsou pouze systémy Windows a Linux. |
| 9006: adresa URL pro stažení souboru metadat z hosta je prázdná. | K tomu může dojít, pokud agent zjišťování nepracuje podle očekávání. | Problém by měl automaticky vyřešit in24 hodiny. Pokud se problém nevyřeší, obraťte se na podpora Microsoftu. |
| 9007: proces spuštění úlohy zjišťování na hostovaném serveru se nenašel. | K tomu může dojít, pokud agent zjišťování nepracuje správně. | Problém by se měl automaticky vyřešit za 24 hodin. Pokud se problém nevyřeší, obraťte se na podpora Microsoftu. |
| 9008: nelze načíst stav procesu hostovaného serveru. | K tomuto problému může dojít z důvodu vnitřní chyby. | Problém by se měl automaticky vyřešit za 24 hodin. Pokud se problém nevyřeší, obraťte se na podpora Microsoftu. |
| 9009: řízení uživatelských účtů systému Windows zabránilo spuštění úlohy zjišťování na serveru. | Nastavení řízení uživatelských účtů systému Windows (UAC) na serveru jsou omezující a zabraňují zjišťování nainstalovaného inventáře softwaru. | V nastavení řízení uživatelských účtů na serveru nakonfigurujte nastavení nástroje řízení uživatelských účtů na jednu z nižších dvou úrovní. |
| 9010: Server je vypnutý. | Server je vypnutý. | Zajistěte, aby byl server zapnutý. |
| 9011: zjištěn soubor metadat nebyl nalezen v systému souborů hostovaného serveru. | K tomuto problému může dojít z důvodu vnitřní chyby. | Problém by se měl automaticky vyřešit za 24 hodin. Pokud se problém nevyřeší, obraťte se na podpora Microsoftu. |
| 9012: soubor metadat byl zjištěn jako prázdný. | K tomuto problému může dojít z důvodu vnitřní chyby. | Problém by se měl automaticky vyřešit za 24 hodin. Pokud se problém nevyřeší, obraťte se na podpora Microsoftu. |
| 9013: pro každé přihlášení je vytvořen nový dočasný profil. | Vytvoří se nový dočasný profil pro každé přihlášení k serveru na VMware. | Pro řešení kontaktujte podpora Microsoftu. |
| 9014: Nepodařilo se načíst metadata ze systému souborů hostovaného serveru. | Žádné připojení k hostiteli ESXi | Ujistěte se, že se zařízení může připojit k portu 443 na hostiteli ESXi, na kterém běží server. |
| 9015: role operací hosta není v uživatelském účtu vCenter povolena. | Role operace hosta není povolena na uživatelském účtu vCenter. | Zajistěte, aby byla na uživatelském účtu vCenter povolená role operace hosta. |
| 9016: nelze zjistit, protože agent operací hosta není aktuální. | Nástroje VMware nejsou správně nainstalované nebo nejsou aktuální. | Ujistěte se, že jsou nástroje VMware správně nainstalované a aktuální. |
| 9017: soubor se zjištěnými metadaty se na serveru nepovedlo najít. | K tomuto problému může dojít z důvodu vnitřní chyby. | Pro řešení kontaktujte podpora Microsoftu. |
| 9018: prostředí PowerShell není nainstalováno na hostovaných serverech. | Prostředí PowerShell není k dispozici na hostovaném serveru. | Nainstalujte PowerShell na hostovaný Server. |
| 9019: nelze zjistit z důvodu selhání operace hostovaného serveru. | Operace hosta VMware se na serveru nezdařila. | Ujistěte se, že přihlašovací údaje serveru jsou platné a že uživatelské jméno zadané v přihlašovacích údajích serveru hosta je ve formátu UPN. |
| 9020: oprávnění k vytvoření souboru bylo odepřeno. | Role přidružená k uživateli nebo zásadám skupiny omezuje uživatele na vytvoření souboru ve složce. | Zkontroluje, jestli zadaný uživatel typu host má oprávnění k vytvoření souboru ve složce. Zobrazit **oznámení** v Azure Migrate: zjišťování a vyhodnocení názvu složky. |
| 9021: v dočasné cestě k systému nelze vytvořit soubor. | Nástroj VMware hlásí systémovou dočasnou cestu namísto dočasné cesty uživatele. | Upgradujte si verzi nástroje VMware nad 10287 (formát klienta NGC/VI). |
| 9022: přístup k objektu WMI byl odepřen. | Role přidružená k uživateli nebo zásadám skupiny omezuje uživateli přístup k objektu rozhraní WMI. | Kontaktujte prosím podpora Microsoftu. |
| 9023: nelze spustit prostředí PowerShell, protože hodnota proměnné prostředí SystemRoot je prázdná. | Hodnota proměnné prostředí SystemRoot je pro hostovaný Server prázdná. | Pro řešení kontaktujte podpora Microsoftu. |
| 9024: nelze zjistit, že hodnota proměnné prostředí TEMP je prázdná. | Hodnota proměnné prostředí TEMP je pro hostovaný Server prázdná. | Kontaktujte prosím podpora Microsoftu. |
| 9025: na hostovaných serverech je prostředí PowerShell poškozené. | V hostovaném serveru je prostředí PowerShell poškozené. | Nainstalujte znovu prostředí PowerShell na serveru hosta a ověřte, zda lze spustit prostředí PowerShell na hostovaném serveru. |
| 9026: na serveru nelze spustit operace hosta. | Stav serveru neumožňuje spuštění operací hosta na serveru. | Pro řešení kontaktujte podpora Microsoftu. |
| 9027: na serveru není spuštěný agent operací hosta. | Nepovedlo se kontaktovat agenta operací hosta běžícího na virtuálním serveru. | Pro řešení kontaktujte podpora Microsoftu. |
| 9028: soubor nelze vytvořit z důvodu nedostatku místa na disku na serveru. | Na disku není dostatek místa. | Zajistěte, aby bylo v diskovém úložišti serveru k dispozici dostatek místa. |
| 9029: nebyl zadán přístup k PowerShellu na základě zadaných přihlašovacích údajů k hostovanému serveru. | Přístup k PowerShellu není pro uživatele k dispozici. | Ujistěte se, že uživatel přidaný do zařízení má přístup k PowerShellu na hostovaném serveru. |
| 9030: nelze shromáždit zjištěná metadata, protože hostitel ESXi je odpojen. | Hostitel ESXi je v odpojeném stavu. | Ujistěte se, že je hostitel ESXi, na kterém běží server, připojený. |
| 9031: nelze shromáždit zjištěná metadata, protože hostitel ESXi neodpovídá. | Vzdálený hostitel je v neplatném stavu. | Zajistěte, aby byl hostitel ESXi, na kterém běží server, spuštěný a připojený. |
| 9032: kvůli vnitřní chybě se nepovedlo zjistit. | K tomuto problému může dojít z důvodu vnitřní chyby. | Pro řešení kontaktujte podpora Microsoftu. |
| 9033: nelze zjistit, protože uživatelské jméno serveru obsahuje neplatné znaky. | V uživatelském jménu byly zjištěny neplatné znaky. | Znovu zadejte přihlašovací údaje serveru, aby nedocházelo k žádným neplatným znakům. |
| 9034: zadané uživatelské jméno není ve formátu UPN. | Uživatelské jméno není ve formátu UPN. | Ujistěte se, že uživatelské jméno je ve formátu hlavního názvu uživatele (UPN). |
| 9035: nelze zjistit, protože režim jazyka PowerShell není nastaven na hodnotu Full Language. | Jazykový režim pro PowerShell na serveru hosta není nastavený na úplný jazyk. | Ujistěte se, že je režim jazyka PowerShell nastavený na úplný jazyk. |
| 9037: shromažďování dat je dočasně pozastaveno, protože doba odezvy serveru je příliš vysoká. | Zjištěný Server trvá příliš dlouho, než odpoví | Nevyžaduje se žádná akce. Pro zjišťování inventáře softwaru a za 3 hodiny pro analýzu závislostí (bez agentů) se pokus o opakování provede za 24 hodin. |
| 10000: typ operačního systému se nepodporuje. | Operační systém spuštěný na serveru není ani Windows ani Linux. | Podporované typy operačních systémů jsou pouze systémy Windows a Linux. |
| 10001: skript pro zjišťování serveru se v zařízení nenašel. | Zjišťování nefunguje podle očekávání. | Pro řešení kontaktujte podpora Microsoftu. |
| 10002: úloha zjišťování nebyla dokončena včas. | Agent zjišťování nepracuje podle očekávání. | Problém by se měl automaticky vyřešit za 24 hodin. Pokud se problém nevyřeší, obraťte se na podpora Microsoftu. |
| 10003: proces provádění úlohy zjišťování byl ukončen s chybou. | Proces provádění úlohy zjišťování byl ukončen s chybou. | Problém by se měl automaticky vyřešit za 24 hodin. Pokud problém přetrvává, kontaktujte prosím podpora Microsoftu. |
| 10004: nebylo zadáno pověření pro typ hostovaného operačního systému. | Přihlašovací údaje pro přístup k serverům tohoto typu operačního systému nebyly v zařízení Azure Migrate k dispozici. | Přidat přihlašovací údaje pro servery na zařízení |
| 10005: zadané přihlašovací údaje nejsou platné. | Přihlašovací údaje zadané pro zařízení pro přístup na server nejsou správné. | Aktualizujte přihlašovací údaje, které jste zadali v zařízení, a ujistěte se, že je server přístupný pomocí přihlašovacích údajů. |
| 10006: typ hostovaného operačního systému není podporovaný úložištěm přihlašovacích údajů. | Operační systém spuštěný na serveru není ani Windows ani Linux. | Podporované typy operačních systémů jsou pouze systémy Windows a Linux. |
| 10007: Nepodařilo se zpracovat metadata zjištěná. | Při pokusu o deserializaci formátu JSON došlo k chybě. | Pro řešení kontaktujte podpora Microsoftu. |
| 10008: na serveru nelze vytvořit soubor. | K tomuto problému může dojít z důvodu vnitřní chyby. | Pro řešení kontaktujte podpora Microsoftu. |
| 10009: Nepodařilo se zapsat zjištěná metadata do souboru na serveru. | K tomuto problému může dojít z důvodu vnitřní chyby. | Pro řešení kontaktujte podpora Microsoftu. |

## <a name="next-steps"></a>Další kroky

Nastavte zařízení pro [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md)nebo [fyzické servery](how-to-set-up-appliance-physical.md).
