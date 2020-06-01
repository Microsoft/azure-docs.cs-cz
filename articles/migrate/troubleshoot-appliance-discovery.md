---
title: Řešení potíží s nasazením a zjišťováním Azure Migrate zařízení
description: Získejte pomoc s nasazením Azure Migrate zařízení a zjišťováním počítačů.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 3d9e4e54d2b1186278afc72c72cdd6bcf33dd41b
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235461"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Řešení potíží s Azure Migrate zařízením a zjišťováním

Tento článek vám pomůže při řešení potíží při nasazování zařízení [Azure Migrate](migrate-services-overview.md) a používání zařízení ke zjišťování místních počítačů.


## <a name="whats-supported"></a>Co je podporováno?

[Zkontrolujte](migrate-appliance.md) požadavky na podporu zařízení.


## <a name="invalid-ovf-manifest-entry"></a>Neplatný záznam manifestu OVF

Pokud se zobrazí chyba "zadaný soubor manifestu je neplatný: Neplatná položka manifestu OVF", udělejte toto:

1. Zkontrolujte jeho hodnotu hash tak, že zkontrolujete, jestli se soubor vajíček Azure Migrate zařízení správně stáhl. [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Pokud hodnota hash neodpovídá, Stáhněte si znovu soubor vajíček a spusťte nasazení znovu.
2. Pokud se nasazení stále nedaří a k nasazení souboru OVF používáte klienta VMware vSphere, zkuste ho nasadit prostřednictvím webového klienta vSphere. Pokud nasazení ještě neproběhne úspěšně, zkuste použít jiný webový prohlížeč.
3. Pokud používáte webového klienta vSphere a pokusíte se ho nasadit na vCenter Server 6,5 nebo 6,7, zkuste nasadit VAJÍČKu přímo na hostiteli ESXi:
   - Připojte se k hostiteli ESXi přímo (místo vCenter Server) s webovým klientem ( *IP adresa hostitele* https://<>/UI).
   - V **domovském**  >  **inventáři**vyberte **soubor**  >  **nasadit šablonu OVF**. Přejděte k VAJÍČKám a dokončete nasazení.
4. Pokud se nasazení stále nedaří, obraťte se na podporu Azure Migrate.

## <a name="cant-connect-to-the-internet"></a>Nejde se připojit k Internetu.

K tomu může dojít, pokud je počítač zařízení za proxy serverem.

- Ujistěte se, že přihlašovací údaje pro autorizaci zadáte, pokud je proxy vyžaduje.
- Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, přidejte [tyto adresy URL](migrate-appliance.md#url-access) do seznamu povolených.
- Pokud pro připojení k Internetu používáte zachycení proxy serveru, importujte certifikát proxy serveru do virtuálního počítače zařízení pomocí [těchto kroků](https://docs.microsoft.com/azure/migrate/concepts-collector).

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Z webové aplikace zařízení se nejde přihlásit k Azure.

Pokud k přihlášení do Azure používáte nesprávný účet Azure, zobrazí se chyba "je nám líto, ale došlo k potížím s přihlášením". K této chybě dochází z několika důvodů:

- Pokud se přihlásíte k webové aplikaci zařízení pro veřejný cloud, použijte přihlašovací údaje uživatelského účtu pro cloudový portál pro státní správu.
- Pokud se přihlásíte k webové aplikaci zařízení pro oficiální Cloud pomocí přihlašovacích údajů uživatelského účtu pro portál privátního cloudu.

Ujistěte se, že používáte správné přihlašovací údaje.

##  <a name="datetime-synchronization-error"></a>Chyba synchronizace data a času

Chyba synchronizace data a času (802) znamená, že hodiny serveru pravděpodobně nejsou synchronizovány s aktuálním časem více než pěti minutami. Změňte čas hodin na virtuálním počítači kolektoru tak, aby odpovídal aktuálnímu času:

1. Otevřete příkazový řádek správce na virtuálním počítači.
2. Chcete-li kontrolovat časové pásmo, spusťte příkaz **W32tm/TZ**.
3. Chcete-li synchronizovat čas, spusťte příkaz **W32tm/Resync**.


## <a name="unabletoconnecttoserver"></a>"UnableToConnectToServer"

Pokud se zobrazí tato chyba připojení, možná se nebudete moci připojit k vCenter Server *servername*. com: 9443. Podrobnosti o chybě označují, že se na něj nenaslouchá žádný koncový bod `https://\*servername*.com:9443/sdk` , který by mohl zprávu přijmout.

- Ověřte, zda používáte nejnovější verzi zařízení. Pokud nejste, upgradujte zařízení na [nejnovější verzi](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Pokud problém pořád proběhne v nejnovější verzi, zařízení nemusí být schopné přeložit zadaný vCenter Server název, nebo je možné, že je zadaný port nesprávný. Ve výchozím nastavení se kolektor pokusí připojit k portu číslo 443, pokud není zadaný port.

    1. Ověřte z zařízení parametr *servername*. com.
    2. Pokud se krok 1 nezdařil, zkuste se připojit k serveru vCenter pomocí IP adresy.
    3. Identifikujte správné číslo portu pro připojení k vCenter Server.
    4. Ověřte, že je vCenter Server v provozu.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Chyba 60052/60039: zařízení pravděpodobně není zaregistrováno.

- Chyba 60052: Pokud účet Azure, který se používá k registraci zařízení, nemá dostatečná oprávnění, "zařízení se nemusí úspěšně zaregistrovat do Azure Migrate projektu".
    - Ujistěte se, že uživatelský účet Azure použitý k registraci zařízení má alespoň oprávnění přispěvatele k tomuto předplatnému.
    - [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) o požadovaných rolích a oprávněních Azure.
- Chyba 60039, "zařízení se nemusí úspěšně zaregistrovat do Azure Migrate projektu" může dojít v případě, že se registrace nezdaří, protože Azure Migrate projekt, který se použil k registraci zařízení, se nepovedlo najít.
    - V Azure Portal a ověřte, zda projekt existuje ve skupině prostředků.
    - Pokud projekt neexistuje, vytvořte ve skupině prostředků nový Azure Migrate projekt a znovu zaregistrujte zařízení. [Přečtěte si, jak](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) vytvořit nový projekt.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Chyba 60030/60031: operace správy Key Vault nebyla úspěšná.

Pokud se zobrazí chyba 60030 nebo 60031, "operace správy Azure Key Vault nebyla úspěšná", udělejte toto:
- Ujistěte se, že uživatelský účet Azure použitý k registraci zařízení má alespoň oprávnění přispěvatele k tomuto předplatnému.
- Ujistěte se, že má účet přístup k trezoru klíčů, který je zadaný v chybové zprávě, a pak zkuste operaci zopakovat.
- Pokud potíže trvají, obraťte se na podporu Microsoftu.
- [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) o požadovaných rolích a oprávněních Azure.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Chyba 60028: zjišťování se nepovedlo inicializovat.

Chyba 60028: zjišťování nelze iniciovat z důvodu chyby. Operace se pro zadaný seznam hostitelů nebo clusterů nezdařila. znamená to, že zjišťování nebylo možné spustit na hostitelích uvedených v této chybě z důvodu problému s přístupem nebo načítáním informací o virtuálním počítači. Zbytek hostitelů byl úspěšně přidán.

- Přidejte hostitele uvedené v této chybě znovu pomocí možnosti **Přidat hostitele** .
- Pokud dojde k chybě ověřování, přečtěte si pokyny k nápravě a opravte chyby a potom zkuste znovu **spustit možnost Uložit a spustit zjišťování** .

## <a name="error-60025-azure-ad-operation-failed"></a>Chyba 60025: operace Azure AD selhala 
Chyba 60025: operace Azure AD se nezdařila. K chybě při vytváření nebo aktualizaci aplikace Azure AD dojde v případě, že se uživatelský účet Azure, který se používá k zahájení zjišťování, liší od účtu použitého k registraci zařízení. Proveďte některou z následujících akcí:

- Ujistěte se, že uživatelský účet, který spouští zjišťování, je stejný jako ten, který se používá k registraci zařízení.
- Zadejte Azure Active Directory oprávnění k přístupu k aplikacím pro uživatelský účet, pro který se operace zjišťování nedaří.
- Odstraňte skupinu prostředků vytvořenou dříve pro Azure Migrate projekt. Vytvořte novou skupinu prostředků a začněte znovu.
- [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) o Azure Active Directory oprávnění aplikace.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Chyba 50004: Nelze se připojit k hostiteli nebo clusteru

Chyba 50004: Nelze se připojit k hostiteli nebo clusteru, protože název serveru nelze přeložit. Kód chyby WinRM: 0x803381B9 se může vyskytnout, pokud služba Azure DNS pro zařízení nemůže vyřešit zadaný název clusteru nebo hostitele.

- Pokud se tato chyba zobrazuje v clusteru, plně kvalifikovaný název domény clusteru.
- Tato chyba se může zobrazit také pro hostitele v clusteru. To znamená, že zařízení se může připojit ke clusteru, ale cluster vrátí názvy hostitelů, které nejsou plně kvalifikované názvy domén. Chcete-li tuto chybu vyřešit, aktualizujte soubor hostitelů na zařízení přidáním mapování IP adresy a názvů hostitelů:
    1. Otevřete Poznámkový blok jako správce.
    2. Otevřete soubor C:\Windows\System32\Drivers\etc\hosts.
    3. Přidejte IP adresu a název hostitele do řádku. Opakujte pro každého hostitele nebo cluster, kde se zobrazí tato chyba.
    4. Uložte a zavřete soubor hostitelů.
    5. Ověřte, jestli se zařízení může připojit k hostitelům pomocí aplikace pro správu zařízení. Po 30 minutách byste měli vidět nejnovější informace pro tyto hostitele v Azure Portal.

## <a name="discovered-vms-not-in-portal"></a>Zjištěné virtuální počítače, které nejsou na portálu

Pokud je stav zjišťování "Probíhá zjišťování", ale tyto virtuální počítače ještě nejsou na portálu zobrazeny, počkejte několik minut:
- Virtuální počítače VMware trvá přibližně 15 minut.
- U každého přidaného hostitele pro zjišťování virtuálních počítačů Hyper-V trvá přibližně dvě minuty.

Pokud počkáte a stav se nezmění, vyberte **aktualizovat** na kartě **servery** . Mělo by se zobrazit počet zjištěných serverů v Azure Migrate: vyhodnocení serveru a Azure Migrate: Migrace serveru.

Pokud to nefunguje a chystáte se zjišťovat servery VMware:

- Ověřte, že zadaný účet vCenter má správně nastavená oprávnění, a to s přístupem k aspoň jednomu virtuálnímu počítači.
- Azure Migrate nemůžou zjistit virtuální počítače VMware, pokud má účet vCenter udělený přístup na úrovni složky virtuálních počítačů vCenter. [Přečtěte si další informace](set-discovery-scope.md) o zjišťování rozsahu.

## <a name="vm-data-not-in-portal"></a>Data virtuálních počítačů nejsou na portálu

Pokud se zjištěné virtuální počítače nezobrazí na portálu nebo pokud jsou data virtuálního počítače zastaralá, počkejte několik minut. Na portálu trvá až 30 minut, než se změny zjištěných konfiguračních dat virtuálního počítače zobrazí na portálu. Může trvat několik hodin, než se změny dat aplikace zobrazí. Pokud po této době žádná data neexistují, zkuste aktualizovat následujícím způsobem.

1. V **Servers**  >  **Azure Migrate servery vyhodnocování serveru**vyberte **Přehled**.
2. V části **Spravovat**vyberte **Agent Health**.
3. Vyberte **aktualizovat agenta**.
4. Počkejte na dokončení operace aktualizace. Nyní byste měli vidět aktuální informace.

## <a name="deleted-vms-appear-in-portal"></a>Odstraněné virtuální počítače se zobrazí na portálu.

Pokud virtuální počítače odstraníte a pořád se zobrazí na portálu, počkejte 30 minut. Pokud se pořád zobrazují, aktualizujte je tak, jak je popsáno výše.

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>Chyba: nahraný soubor nemá očekávaný formát.
Některé nástroje mají místní nastavení, které vytvoří soubor CSV se středníkem jako oddělovačem. Změňte prosím nastavení tak, aby byl oddělovač čárkou.

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>Naimportoval (a) jsem sdílený svazek clusteru, ale zobrazuje se stav zjišťování probíhá.
Tento stav se zobrazí, pokud selhalo nahrávání sdíleného svazku clusteru z důvodu chyby ověření. Pokuste se znovu naimportovat sdílený svazek clusteru. Můžete si stáhnout zprávu o chybách předchozího nahrávání a pomocí pokynů k nápravě v souboru tyto chyby opravit. Zprávu o chybách si můžete stáhnout z části Import podrobností na stránce zjistit počítače.

## <a name="do-not-see-application-details-even-after-updating-guest-credentials"></a>Nevidíte podrobnosti o aplikacích ani po aktualizaci přihlašovacích údajů hostů.
Zjišťování aplikací se spouští každých 24 hodin. Chcete-li zobrazit podrobnosti okamžitě, proveďte aktualizaci následujícím způsobem. To může trvat několik minut v závislosti na ne. zjištěných virtuálních počítačů.

1. V **Servers**  >  **Azure Migrate servery vyhodnocování serveru**vyberte **Přehled**.
2. V části **Spravovat**vyberte **Agent Health**.
3. Vyberte **aktualizovat agenta**.
4. Počkejte na dokončení operace aktualizace. Nyní byste měli vidět aktuální informace.

## <a name="unable-to-export-application-inventory"></a>Inventář aplikací se nedá exportovat.
Zajistěte, aby uživatel stáhl inventář z portálu má oprávnění přispěvatele k tomuto předplatnému.

## <a name="common-app-discovery-errors"></a>Běžné chyby zjišťování aplikací

Azure Migrate podporuje zjišťování aplikací, rolí a funkcí pomocí Azure Migrate: posouzení serveru. Zjišťování aplikací se v současné době podporuje jenom pro VMware. [Přečtěte si další informace](how-to-discover-applications.md) o požadavcích a krocích pro nastavení zjišťování aplikací.

Typické chyby zjišťování aplikací jsou shrnuté v tabulce. 

**Chyba** | **Příčina** | **Akce**
--- | --- | --- | ---
10000: nepovedlo se zjistit aplikace nainstalované na serveru. | K tomu může dojít, pokud operační systém počítače není Windows nebo Linux. | Používejte jenom zjišťování aplikací pro Windows/Linux.
10001: nelze načíst aplikace nainstalované serverem. | Vnitřní chyba – některé chybějící soubory v zařízení. | Obraťte se na podporu Microsoftu.
10002: nelze načíst aplikace nainstalované serverem. | Agent zjišťování na zařízení nemusí správně fungovat. | Pokud se problém nepodaří vyřešit během 24 hodin, obraťte se na podporu.
10003 "nepovedlo se načíst aplikace nainstalovaná na serveru" | Agent zjišťování na zařízení nemusí správně fungovat. | Pokud se problém nepodaří vyřešit během 24 hodin, obraťte se na podporu.
10004: "nepovedlo se zjistit nainstalované aplikace pro <počítače> Windows/Linux". |  Přihlašovací údaje pro přístup k <počítačům> systému Windows/Linux nejsou v zařízení k dispozici.| Přidejte přihlašovací údaje k zařízení, které má přístup k <počítačům> Windows/Linux.
10005: nejde získat přístup k místnímu serveru. | Přístup k přihlašovacím údajům může být chybný. | Aktualizujte přihlašovací údaje zařízení a ujistěte se, že k příslušnému počítači máte přístup. 
10006: nejde získat přístup k místnímu serveru. | K tomu může dojít, pokud operační systém počítače není Windows nebo Linux.|  Používejte jenom zjišťování aplikací pro Windows/Linux.
10007: Nepodařilo se zpracovat načtená metadata. | K této vnitřní chybě došlo při pokusu o deserializaci JSON. | Kontaktování podpora Microsoftu pro řešení
9000/9001/9002: nepovedlo se zjistit aplikace nainstalované na serveru. | Nástroje VMware pravděpodobně nejsou nainstalovány nebo jsou poškozeny. | Na příslušný počítač nainstalujte nebo přeinstalujte nástroje VMware a ověřte, že je spuštěný.
9003: nepovedlo se zjistit aplikace nainstalované na serveru. | K tomu může dojít, pokud operační systém počítače není Windows nebo Linux. | Používejte jenom zjišťování aplikací pro Windows/Linux.
9004: nepovedlo se zjistit aplikace nainstalované na serveru. | K tomu může dojít v případě, že je virtuální počítač vypnutý. | Pro zjišťování se ujistěte, že je virtuální počítač zapnutý.
9005: nepovedlo se zjistit aplikace nainstalované na virtuálním počítači. | K tomu může dojít, pokud operační systém počítače není Windows nebo Linux. | Používejte jenom zjišťování aplikací pro Windows/Linux.
9006/9007: nelze načíst aplikace nainstalované serverem. | Agent zjišťování na zařízení nemusí správně fungovat. | Pokud se problém nepodaří vyřešit během 24 hodin, obraťte se na podporu.
9008: nelze načíst aplikace nainstalované serverem. | Může se jednat o vnitřní chybu.  | TF problém se nedokáže vyřešit během 24 hodin, obraťte se na podporu.
9009: nelze načíst aplikace nainstalované serverem. | Může dojít v případě, že nastavení řízení uživatelských účtů (UAC) systému Windows na serveru jsou omezující a znemožňuje zjišťování nainstalovaných aplikací. | Vyhledejte nastavení řízení uživatelských účtů na serveru a nakonfigurujte nastavení nástroje řízení uživatelských účtů na serveru na jednu z nižších dvou úrovní.
9010: virtuální počítač je vypnutý. | Virtuální počítač je vypnutý.  | Ujistěte se, že je virtuální počítač zapnutý.
9011: soubor ke stažení z hosta se nenašel na virtuálním počítači hosta. | K tomuto problému může dojít z důvodu vnitřní chyby. | Problém by se měl automaticky vyřešit během 24 hodin. Pokud problém přetrvává, kontaktujte prosím podpora Microsoftu.
9012: obsah souboru výsledků je prázdný. | K tomuto problému může dojít z důvodu vnitřní chyby. | Problém by se měl automaticky vyřešit během 24 hodin. Pokud problém přetrvává, kontaktujte prosím podpora Microsoftu.
9013: "pro každé přihlášení k virtuálnímu počítači VMware je vytvořen nový dočasný profil" | Pro každé přihlášení k virtuálnímu počítači se vytvoří nový dočasný profil. | Ujistěte se, že uživatelské jméno zadané v přihlašovacích údajích k hostovanému virtuálnímu počítači je ve formátu UPN.
9014: Nepodařilo se načíst metadata ze systému souborů hostovaného virtuálního počítače. | Došlo k potížím při připojování k hostiteli ESXi. | Ujistěte se, že se zařízení může připojit k portu 443 na hostiteli ESXi, na kterém běží virtuální počítač.
9015: Nelze se připojit k virtuálním počítačům VMware z důvodu nedostatečných oprávnění na vCenter. | Role operace hosta není v uživatelském účtu vCenter povolena. | Zajistěte, aby byla na uživatelském účtu vCenter povolená role operace hosta.
9016: nejde se připojit k virtuálním počítačům VMware, protože agent operací hosta je mimo data. | Nástroje VMware nejsou správně nainstalované nebo nejsou aktuální. | Ujistěte se, že jsou nástroje VMware správně nainstalované a aktuální.
9017: na virtuálním počítači se nenašel soubor se zjištěnými metadaty. | K tomuto problému může dojít z důvodu vnitřní chyby. | Pro řešení kontaktujte podpora Microsoftu.
9018: PowerShell není nainstalovaný na virtuálních počítačích hosta. | Prostředí PowerShell není v hostovaném virtuálním počítači k dispozici. | Nainstalujte PowerShell do virtuálního počítače hosta.
9019: "nelze zjistit z důvodu selhání operací virtuálních počítačů hosta" | Operace hosta VMware se na virtuálním počítači nezdařila. | Ujistěte se, že přihlašovací údaje virtuálního počítače jsou platné a že uživatelské jméno zadané v přihlašovacích údajích k hostovanému virtuálnímu počítači má formát UPN.
9020: oprávnění k vytvoření souboru bylo odepřeno. | Role přidružená k uživateli nebo zásadám skupiny omezuje uživatele na vytvoření souboru ve složce. | Zkontroluje, jestli zadaný uživatel typu host má oprávnění k vytvoření souboru ve složce. Viz **oznámení** v tématu vyhodnocování serveru pro název složky.
9021: v dočasné cestě systému složek se zamítlo oprávnění k vytvoření souboru. | Verze nástroje VMware na virtuálním počítači není podporovaná. | Upgradujte si verzi nástroje VMware nad 10.2.0.
9022: "získat přístup k objektům WMI byl odepřen" | Role přidružená k uživateli nebo zásadám skupiny omezuje uživatele na přístup k objektu rozhraní WMI. | Kontaktujte prosím podpora Microsoftu.
9023: hodnota proměnné prostředí SystemRoot je prázdná. | Neznámo | Kontaktujte prosím podpora Microsoftu.
9024: hodnota proměnné prostředí TEMP je prázdná. | Neznámo | Kontaktujte prosím podpora Microsoftu.
9025: "prostředí PowerShell je poškozeno na virtuálních počítačích hosta" | Neznámo | Přeinstalujte PowerShell v hostovaném virtuálním počítači a ověřte, jestli je možné spustit PowerShell na virtuálním počítači hosta.
8084: nepovedlo se zjistit aplikace z důvodu chyby VMware  <Exception from VMware> : | Zařízení Azure Migrate používá rozhraní API VMware ke zjišťování aplikací. K tomuto problému může dojít, pokud se při pokusu o zjištění aplikací vyvolá výjimka vCenter Server. Zpráva o chybě z VMware se zobrazí v chybové zprávě zobrazené na portálu. | Vyhledejte zprávu v [dokumentaci k VMware](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)a proveďte opravu pomocí těchto kroků. Pokud nemůžete opravit, obraťte se na podporu Microsoftu.



## <a name="next-steps"></a>Další kroky
Nastavte zařízení pro [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md)nebo [fyzické servery](how-to-set-up-appliance-physical.md).
