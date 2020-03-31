---
title: Poradce při potížích s nasazením a zjišťováním zařízení Azure Migrate
description: Získejte pomoc s nasazením zařízení Azure Migrate a zjišťováním počítačů.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 9fbf55fbe16d958bf10541894159dade26668bef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336729"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Poradce při potížích s zařízením a zjišťováním migrace Azure

Tento článek vám pomůže vyřešit problémy při nasazování zařízení [Azure Migrate](migrate-services-overview.md) a pomocí zařízení ke zjišťování místních počítačů.


## <a name="whats-supported"></a>Co je podporováno?

[Zkontrolujte](migrate-appliance.md) požadavky na podporu spotřebiče.


## <a name="invalid-ovf-manifest-entry"></a>"Neplatná položka manifestu OVF"

Pokud se zobrazí chyba "Poskytnutý soubor manifestu je neplatný: Neplatná položka manifestu OVF", postupujte takto:

1. Zkontrolujte, zda je soubor zařízení Azure Migrate AZDU správně stažen, a to tak, že zkontrolujete jeho hodnotu hash. [Další informace](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Pokud se hodnota hash neshoduje, stáhněte soubor OVA znovu a opakujte nasazení.
2. Pokud nasazení stále selže a používáte klienta VMware vSphere k nasazení souboru OVF, zkuste ho nasadit prostřednictvím webového klienta vSphere. Pokud nasazení stále selže, zkuste použít jiný webový prohlížeč.
3. Pokud používáte webového klienta vSphere a pokoušíte se ho nasadit na vCenter Server 6.5 nebo 6.7, zkuste nasadit OVA přímo na hostitele ESXi:
   - Připojte se k hostiteli ESXi přímo (namísto vCenter Serveru) pomocí webového klienta (https://<*ip adresa hostitele*>/ui).
   - V **části Domácí** > **inventář**vyberte**šablonu OVF nasazení** **souborů** > . Přejděte na OVA a dokončete nasazení.
4. Pokud se nasazení stále nezdaří, obraťte se na podporu Migrace Azure.

## <a name="cant-connect-to-the-internet"></a>Nelze se připojit k internetu

K tomu může dojít, pokud je zařízení za proxy serverem.

- Ujistěte se, že jste zadali autorizační pověření, pokud je proxy server potřebuje.
- Pokud k řízení odchozího připojení používáte proxy firewall založený na adrese URL, přidejte tyto adresy URL do seznamu [povolených](migrate-appliance.md#url-access) adres.
- Pokud používáte zachycující proxy pro připojení k Internetu, importujte proxy certifikát na virtuální počítač zařízení pomocí [těchto kroků](https://docs.microsoft.com/azure/migrate/concepts-collector).

##  <a name="datetime-synchronization-error"></a>Chyba synchronizace data a času

Chyba o synchronizaci data a času (802) označuje, že hodiny serveru mohou být mimo synchronizaci s aktuálním časem o více než pět minut. Změňte čas hodin na virtuálním počítači kolektoru tak, aby odpovídal aktuálnímu času:

1. Otevřete příkazový řádek správce na virtuálním počítači.
2. Chcete-li zkontrolovat časové pásmo, spusťte **w32tm /tz**.
3. Chcete-li synchronizovat čas, spusťte **w32tm /resync**.


## <a name="unabletoconnecttoserver"></a>"Nelze připojit k serveru"

Pokud se zobrazí tato chyba připojení, může se stát, že se nebudete moci připojit k názvu serveru vCenter Server .com:9443. *Servername* Podrobnosti o chybě označují, že neexistuje `https://\*servername*.com:9443/sdk` žádný koncový bod naslouchání, které můžete přijmout zprávu.

- Zkontrolujte, zda používáte nejnovější verzi zařízení. Pokud nejste, upgradujte zařízení na [nejnovější verzi](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Pokud k problému stále dochází v nejnovější verzi, zařízení pravděpodobně nebude možné přeložit zadaný název serveru vCenter nebo zadaný port může být chybný. Ve výchozím nastavení, pokud není zadán port, kolektor se pokusí připojit k portu číslo 443.

    1. Ping *Název serveru*.com ze zařízení.
    2. Pokud se krok 1 nezdaří, zkuste se připojit k serveru vCenter pomocí adresy IP.
    3. Určete správné číslo portu pro připojení k serveru vCenter.
    4. Ověřte, zda je server vCenter v provozu.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Chyba 60052/60039: Zařízení nemusí být zaregistrováno

- Chyba 60052 " Zařízení nemusí být úspěšně zaregistrováno do projektu Azure Migrate" dojde, pokud účet Azure slouží k registraci zařízení nemá dostatečná oprávnění.
    - Ujistěte se, že uživatelský účet Azure používaný k registraci zařízení má alespoň oprávnění přispěvatele k předplatnému.
    - [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) o požadovaných rolích a oprávněních Azure.
- Chyba 60039, "Zařízení nemusí být úspěšně zaregistrováno do projektu Migrace Azure" může dojít, pokud se nezdaří registrace, protože projekt Migrace Azure používá ke žurnálu zařízení nelze najít.
    - Na webu Azure portal a zkontrolujte, zda projekt existuje ve skupině prostředků.
    - Pokud projekt neexistuje, vytvořte nový projekt Azure Migrate ve skupině prostředků a znovu zaregistrujte zařízení. [Přečtěte si, jak](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) vytvořit nový projekt.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Chyba 60030/60031: Operace správy trezoru klíčů se nezdařila.

Pokud se zobrazí chyba 60030 nebo 60031, "Operace správy úložiště klíčů Azure se nezdařilo", postupujte takto:
- Ujistěte se, že uživatelský účet Azure používaný k registraci zařízení má alespoň oprávnění přispěvatele k předplatnému.
- Ujistěte se, že účet má přístup k trezoru klíčů zadanému v chybové zprávě a opakujte operaci.
- Pokud potíže trvají, obraťte se na podporu Microsoftu.
- [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) o požadovaných rolích a oprávněních Azure.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Chyba 60028: Zjišťování nelze spustit

Chyba 60028: "Zjišťování nelze spustit z důvodu chyby. Operace se nezdařila pro zadaný seznam hostitelů nebo clusterů" označuje, že zjišťování nelze spustit na hostitelích uvedených v chybě z důvodu problému s přístupem nebo načítáním informací o virtuálním počítači. Ostatní hostitelé byli úspěšně přidáni.

- Přidejte hostitele uvedené v chybě znovu pomocí možnosti **Přidat hostitele.**
- Pokud se chyba ověření zobrazí, přečtěte si pokyny k nápravě, abyste je opravili, a zkuste znovu **uložit a spustit zjišťování.**

## <a name="error-60025-azure-ad-operation-failed"></a>Chyba 60025: Operace Azure AD se nezdařila. 
Chyba 60025: "Operace služby Azure AD se nezdařila. K chybě došlo při vytváření nebo aktualizaci aplikace Azure AD" dojde, když uživatelský účet Azure slouží k zahájení zjišťování se liší od účtu slouží k registraci zařízení. Proveďte jednu z těchto akcí:

- Ujistěte se, že uživatelský účet iniciující zjišťování je stejný jako účet používaný k registraci zařízení.
- Poskytněte přístupkovým oprávněním aplikací služby Azure Active Directory k uživatelskému účtu, u kterého se nedaří operace zjišťování.
- Odstraňte skupinu prostředků, která byla dříve vytvořena pro projekt Migrace Azure. Vytvořte další skupinu prostředků a začněte znovu.
- [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) o oprávněních aplikací Azure Active Directory.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Chyba 50004: Nelze se připojit k hostiteli nebo clusteru

Chyba 50004: "Nelze se připojit k hostiteli nebo clusteru, protože název serveru nelze přeložit. Kód chyby WinRM: 0x803381B9" může dojít, pokud služba Azure DNS pro zařízení nelze přeložit clusteru nebo názvu hostitele, které jste zadali.

- Pokud se tato chyba zobrazí v clusteru, v clusteru FQDN clusteru.
- Tato chyba se může zobrazit také pro hostitele v clusteru. To znamená, že se zařízení může připojit ke clusteru, ale cluster vrátí názvy hostitelů, které nejsou sítěmi pro co dokovatcí domén. Chcete-li tuto chybu vyřešit, aktualizujte soubor hosts v zařízení přidáním mapování adresy IP a názvů hostitelů:
    1. Otevřete Poznámkový blok jako správce.
    2. Otevřete soubor C:\Windows\System32\Drivers\etc\hosts.
    3. Přidejte adresu IP a název hostitele do řádku. Tento postup opakujte pro každého hostitele nebo clusteru, kde se zobrazí tato chyba.
    4. Uložte a zavřete soubor hostitelů.
    5. Pomocí aplikace pro správu zařízení zkontrolujte, zda se zařízení může připojit k hostitelům. Po 30 minutách byste měli vidět nejnovější informace o těchto hostitelích na webu Azure Portal.

## <a name="discovered-vms-not-in-portal"></a>Zjištěné virtuální chody nejsou na portálu

Pokud je stav zjišťování "Zjišťování probíhá", ale ještě nevidíte virtuální chody na portálu, počkejte několik minut:
- Virtuální měna VMware trvá přibližně 15 minut.
- Trvá přibližně dvě minuty pro každý přidaný hostitel pro zjišťování virtuálních výchv hyper-V.

Pokud počkáte a stav se nezmění, vyberte na kartě **Servery** **možnost Aktualizovat.** To by mělo zobrazit počet zjištěných serverů v Azure Migrate: Vyhodnocení serveru a Migrace Azure: Migrace serveru.

Pokud to nefunguje a objevujete servery VMware:

- Ověřte, zda zadaný účet vCentra má správně nastavená oprávnění s přístupem alespoň k jednomu virtuálnímu virtuálnímu účtu.
- Azure Migrate nemůže zjistit virtuální počítače VMware, pokud má účet vCenter přístup udělený na úrovni složky virtuálních počítačích vCenter. [Další informace](set-discovery-scope.md) o zjišťování oborů.

## <a name="vm-data-not-in-portal"></a>Data virtuálního aplikace, která nejsou na portálu

Pokud zjištěné virtuální chodů nezobrazí na portálu nebo pokud jsou data virtuálního aplikace zastaralá, počkejte několik minut. Trvá až 30 minut, než se změny v zjištěných konfiguračních datech virtuálního počítače zobrazí na portálu. Může trvat několik hodin, než se změny v datech aplikace zobrazí. Pokud po této době nejsou k dispozici žádná data, zkuste aktualizovat následujícím způsobem

1. V **části Servery** > **Azure Migrate Server Assessment**vyberte **Přehled**.
2. V části **Manage**vyberte **položku Stav agenta**.
3. Vyberte **možnost Aktualizovat agenta**.
4. Počkejte na dokončení operace aktualizace. Nyní byste měli vidět aktuální informace.

## <a name="deleted-vms-appear-in-portal"></a>Odstraněné virtuální ms se zobrazují na portálu

Pokud odstraníte virtuální aplikace a stále se zobrazí na portálu, počkejte 30 minut. Pokud se stále zobrazují, aktualizujte, jak je popsáno výše.

## <a name="common-app-discovery-errors"></a>Běžné chyby zjišťování aplikací

Azure Migrate podporuje zjišťování aplikací, rolí a funkcí pomocí Azure Migrate: Server Assessment. Zjišťování aplikací je v současné době podporováno pouze pro společnost VMware. [Přečtěte si další informace](how-to-discover-applications.md) o požadavcích a krocích pro nastavení zjišťování aplikací.

Typické chyby zjišťování aplikací jsou shrnuty v tabulce. 

**Chyba** | **Příčina** | **Akce**
--- | --- | --- | ---
10000: "Nelze zjistit aplikace nainstalované na serveru". | Tato situace může nastat, pokud operační systém počítače není Windows nebo Linux. | Zjišťování aplikací používejte jenom pro Windows/Linux.
10001: "Nelze načíst aplikace nainstalované server". | Vnitřní chyba - některé chybějící soubory v zařízení. | Obraťte se na podporu Microsoftu.
10002: "Nelze načíst aplikace nainstalované server". | Je možné, že agent zjišťování na zařízení nepracuje správně. | Pokud se problém nevyřeší do 24 hodin, obraťte se na podporu.
10003 "Nelze načíst nainstalované aplikace serveru". | Je možné, že agent zjišťování na zařízení nepracuje správně. | Pokud se problém nevyřeší do 24 hodin, obraťte se na podporu.
10004: "Nelze zjistit nainstalované aplikace pro <windows/linux> počítače." |  V zařízení nebyla k dispozici pověření pro přístup <počítače se systémem Windows/Linux>.| Přidejte pověření do zařízení, které má přístup k <> počítačů m windows/linux.
10005: "Nelze získat přístup k místnímu serveru". | Přístupová pověření mohou být chybná. | Aktualizujte přihlašovací údaje zařízení a ujistěte se, že s nimi máte přístup k příslušnému počítači. 
10006: "Nelze získat přístup k místnímu serveru". | Tato situace může nastat, pokud operační systém počítače není Windows nebo Linux.|  Zjišťování aplikací používejte jenom pro Windows/Linux.
10007: "Nelze zpracovat načtená metadata" | Při pokusu o rekonstrukci funkce JSON došlo k této vnitřní chybě. | Obraťte se na podporu společnosti Microsoft pro řešení
9000/9001/9002: "Nelze zjistit aplikace nainstalované na serveru". | Nástroje vmware nemusí být nainstalovány nebo jsou poškozeny. | Nainstalujte/přeinstalujte nástroje VMware na příslušném počítači a zkontrolujte, zda je spuštěn.
9003: Nelze zjistit aplikace nainstalované na serveru". | Tato situace může nastat, pokud operační systém počítače není Windows nebo Linux. | Zjišťování aplikací používejte jenom pro Windows/Linux.
9004: "Nelze zjistit aplikace nainstalované na serveru". | K tomu může dojít, pokud je virtuální virtuální měnový virtuální motor zapnutý. | Pro zjišťování, ujistěte se, že virtuální počítač je zapnutý.
9005: "Nelze zjistit aplikace nainstalované ve virtuálním počítači. | Tato situace může nastat, pokud operační systém počítače není Windows nebo Linux. | Zjišťování aplikací používejte jenom pro Windows/Linux.
9006/9007: "Nelze načíst nainstalované aplikace server". | Je možné, že agent zjišťování na zařízení nepracuje správně. | Pokud se problém nevyřeší do 24 hodin, obraťte se na podporu.
9008: "Nelze načíst nainstalované aplikace serveru". | Může to být vnitřní chyba.  | Tf problém nevyřeší sám do 24 hodin, kontaktujte podporu.
9009: "Nelze načíst nainstalované aplikace serveru". | Může dojít, pokud nastavení řízení uživatelských účtů systému Windows (UAC) na serveru jsou omezující a zabránit zjišťování nainstalovaných aplikací. | Vyhledejte nastavení Řízení uživatelských účtů na serveru a nakonfigurujte nastavení řízení uživatelských účtů na serveru na jednu z nižších dvou úrovní.
9010: "Nelze načíst nainstalované aplikace serveru". | Může to být vnitřní chyba.  | Tf problém nevyřeší sám do 24 hodin, kontaktujte podporu.
9011: "Soubor ke stažení z hosta nebyl nalezen na hostu virtuálního počítači" | K problému může dojít z důvodu vnitřní chyby. | Problém by měl být automaticky vyřešen do 24 hodin. Pokud problém přetrvává, obraťte se na podporu společnosti Microsoft.
9012: "Obsah výsledku souboru jsou prázdné." | K problému může dojít z důvodu vnitřní chyby. | Problém by měl být automaticky vyřešen do 24 hodin. Pokud problém přetrvává, obraťte se na podporu společnosti Microsoft.
9013: "Nový dočasný profil je vytvořen pro každé přihlášení do virtuálního mísu VMware" | Pro každé přihlášení do virtuálního mísa se vytvoří nový dočasný profil. | Ujistěte se, že uživatelské jméno uvedené v pověřeních hostovaného virtuálního uživatele je ve formátu UPN.
9015: "Nelze se připojit k virtuálním počítačům VMware z důvodu nedostatečných oprávnění v centru vcenter" | V uživatelském účtu vcenter není povolena role Operace hosta. | Ujistěte se, že je v uživatelském účtu vCenter povolena role Operace hosta.
9016: "Nelze se připojit k virtuálním virtuálním mům VMware, protože provozní agent hosta je bez dat" | Nástroje vmware nejsou správně nainstalovány nebo nejsou aktuální. | Ujistěte se, že jsou nástroje VMware správně nainstalovány a aktuální.
9017: "Soubor s zjištěná metadata se nenachází na virtuálním počítači." | K problému může dojít z důvodu vnitřní chyby. | Obraťte se na podporu společnosti Microsoft pro řešení.
9018: "Prostředí PowerShell není nainstalované ve virtuálních virtuálních měn hosta." | Prostředí PowerShell není k dispozici ve virtuálním virtuálním ms hosta. | Nainstalujte PowerShell do hostovaného virtuálního soudu.
9019: "Nelze zjistit z důvodu selhání operace virtuálního virtuálního provozu hosta" | Operace hosta vmware se nezdařila na virtuálním počítači. | Ujistěte se, že pověření virtuálního uživatele jsou platná a uživatelské jméno uvedené v pověření hostovaného virtuálního uživatele je ve formátu UPN.
9020: "Oprávnění k vytvoření souboru je odepřeno." | Role přidružená k uživateli nebo zásadám skupiny omezuje uživatele k vytvoření souboru ve složce | Zkontrolujte, zda daný uživatel typu Host nemá oprávnění k vytvoření souboru ve složce. Název složky naleznete v **části Oznámení** v posouzení serveru.
9021: "Oprávnění k vytvoření souboru je odepřeno ve složce System Temp Path." | Verze nástroje VMware na virtuálním počítači není podporovaná | Inovujte verzi nástroje VMware nad 10.2.0.
9022: "Získat přístup k objektům wmi je odepřen." | Role přidružená k uživateli nebo zásadám skupiny omezuje uživatele pro přístup k objektu služby WMI. | Obraťte se na podporu společnosti Microsoft.
9023: "Hodnota proměnné prostředí SystemRoot je prázdná." | Není známo | Obraťte se na podporu společnosti Microsoft.
9024: "Hodnota proměnné prostředí TEMP je prázdná." | Není známo | Obraťte se na podporu společnosti Microsoft.
9025: "Prostředí PowerShell je poškozen ve virtuálních virtuálních měničících." | Není známo | Přeinstalujte PowerShell v hostovaném virtuálním počítači a zkontrolujte, jestli powershell udávaný na hostovaném virtuálním počítači.
8084: "Nelze zjistit aplikace z důvodu <Exception from VMware>chyby VMware: " | Zařízení Migrace Azure používá k zjišťování aplikací api VMware. K tomuto problému může dojít, pokud je vyvolána výjimka vCenter Server při pokusu o zjišťování aplikací. Chybová zpráva od společnosti VMware se zobrazí v chybové zprávě zobrazené na portálu. | Vyhledejte zprávu v dokumentaci společnosti [VMware](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)a postupujte podle pokynů k opravě. Pokud se vám nepodaří opravit, obraťte se na podporu společnosti Microsoft.



## <a name="next-steps"></a>Další kroky
Nastavte zařízení pro servery [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md)nebo [fyzické servery](how-to-set-up-appliance-physical.md).
