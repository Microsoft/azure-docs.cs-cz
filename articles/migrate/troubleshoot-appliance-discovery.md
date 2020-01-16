---
title: Řešení potíží s nasazením a zjišťováním Azure Migrate zařízení
description: Získejte pomoc s nasazením Azure Migrate zařízení a zjišťováním počítačů.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 854d98a4f2a1b2500bc301d2305c0dd1f27aaaa4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980816"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Řešení potíží s Azure Migrate zařízením a zjišťováním

Tento článek vám pomůže při řešení potíží při nasazování zařízení [Azure Migrate](migrate-services-overview.md) a používání zařízení ke zjišťování místních počítačů.


## <a name="whats-supported"></a>Co je podporováno?

[Zkontrolujte](migrate-appliance.md) požadavky na podporu zařízení.


## <a name="invalid-ovf-manifest-entry"></a>Neplatný záznam manifestu OVF

Pokud se zobrazí chyba "zadaný soubor manifestu je neplatný: Neplatná položka manifestu OVF", udělejte toto:

1. Zkontrolujte jeho hodnotu hash tak, že zkontrolujete, jestli se soubor vajíček Azure Migrate zařízení správně stáhl. [Další informace](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Pokud hodnota hash neodpovídá, Stáhněte si znovu soubor vajíček a spusťte nasazení znovu.
2. Pokud se nasazení stále nedaří a k nasazení souboru OVF používáte klienta VMware vSphere, zkuste ho nasadit prostřednictvím webového klienta vSphere. Pokud nasazení ještě neproběhne úspěšně, zkuste použít jiný webový prohlížeč.
3. Pokud používáte webového klienta vSphere a pokusíte se ho nasadit na vCenter Server 6,5 nebo 6,7, zkuste nasadit VAJÍČKu přímo na hostiteli ESXi:
   - Připojte se k hostiteli ESXi přímo (místo vCenter Server) pomocí webového klienta (https://<*IP adresa hostitele*>/UI).
   - V > **inventáře** **domů** vyberte **soubor** > **nasadit šablonu OVF**. Přejděte k VAJÍČKám a dokončete nasazení.
4. Pokud se nasazení stále nedaří, obraťte se na podporu Azure Migrate.

## <a name="cant-connect-to-the-internet"></a>Nejde se připojit k Internetu.

K tomu může dojít, pokud je počítač zařízení za proxy serverem.

- Ujistěte se, že přihlašovací údaje pro autorizaci zadáte, pokud je proxy vyžaduje.
- Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, přidejte tyto adresy URL do seznamu povolených adres:

    - [Adresy URL pro vyhodnocení VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
    - [Adresy URL pro posouzení technologie Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
    - [Adresy URL pro migraci bez agentů VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
    - [Adresy URL pro migraci založené na agentech VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
    - [Adresy URL pro migraci technologie Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

- Pokud pro připojení k Internetu používáte zachycení proxy serveru, importujte certifikát proxy serveru do virtuálního počítače zařízení pomocí [těchto kroků](https://docs.microsoft.com/azure/migrate/concepts-collector).

##  <a name="datetime-synchronization-error"></a>Chyba synchronizace data a času

Chyba synchronizace data a času (802) znamená, že hodiny serveru pravděpodobně nejsou synchronizovány s aktuálním časem více než pěti minutami. Změňte čas hodin na virtuálním počítači kolektoru tak, aby odpovídal aktuálnímu času:

1. Otevřete příkazový řádek správce na virtuálním počítači.
2. Chcete-li kontrolovat časové pásmo, spusťte příkaz **W32tm/TZ**.
3. Chcete-li synchronizovat čas, spusťte příkaz **W32tm/Resync**.


## <a name="unabletoconnecttoserver"></a>"UnableToConnectToServer"

Pokud se zobrazí tato chyba připojení, možná se nebudete moci připojit k vCenter Server *servername*. com: 9443. Podrobnosti o chybě označují, že na https://*servername*. com: 9443/SDK, který může přijmout zprávu, nenaslouchá žádný koncový bod.

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
Chyba 60025: operace Azure AD se nezdařila. K chybě při vytváření nebo aktualizaci aplikace Azure AD dojde v případě, že se uživatelský účet Azure, který se používá k zahájení zjišťování, liší od účtu použitého k registraci zařízení. Proveďte jednu z těchto akcí:

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
    4. Uložte a zavřete soubor hostitelů.
    5. Ověřte, jestli se zařízení může připojit k hostitelům pomocí aplikace pro správu zařízení. Po 30 minutách byste měli vidět nejnovější informace pro tyto hostitele v Azure Portal.

## <a name="discovered-vms-not-in-portal"></a>Zjištěné virtuální počítače, které nejsou na portálu

Pokud je stav zjišťování "Probíhá zjišťování", ale tyto virtuální počítače ještě nejsou na portálu zobrazeny, počkejte několik minut:
- Virtuální počítače VMware trvá přibližně 15 minut.
- U každého přidaného hostitele pro zjišťování virtuálních počítačů Hyper-V trvá přibližně dvě minuty.

Pokud počkáte a stav se nezmění, vyberte **aktualizovat** na kartě **servery** . Mělo by se zobrazit počet zjištěných serverů v Azure Migrate: vyhodnocení serveru a Azure Migrate: Migrace serveru.

Pokud to nefunguje a chystáte se zjišťovat servery VMware:

- Ověřte, že zadaný účet vCenter má správně nastavená oprávnění, a to s přístupem k aspoň jednomu virtuálnímu počítači.
- Azure Migrate nemůžou zjistit virtuální počítače VMware, pokud má účet vCenter udělený přístup na úrovni složky virtuálních počítačů vCenter. [Přečtěte si další informace](tutorial-assess-vmware.md#set-the-scope-of-discovery) o zjišťování rozsahu.

## <a name="vm-data-not-in-portal"></a>Data virtuálních počítačů nejsou na portálu

Pokud se zjištěné virtuální počítače nezobrazí na portálu, počkejte pár minut. Zobrazení zjištěných dat na portálu trvá až 30 minut. Pokud po 30 minutách neexistují žádná data, zkuste aktualizaci provést následujícím způsobem:

1. V > servery **Azure Migrate posouzení serveru**vyberte **Přehled**.
2. V části **Spravovat**vyberte **Agent Health**.
3. Vyberte **aktualizovat agenta**.
4. Počkejte na dokončení operace aktualizace. Nyní byste měli vidět aktuální informace.

## <a name="deleted-vms-appear-in-portal"></a>Odstraněné virtuální počítače se zobrazí na portálu.

Pokud virtuální počítače odstraníte a pořád se zobrazí na portálu, počkejte 30 minut. Pokud se pořád zobrazují, aktualizujte je tak, jak je popsáno výše.

## <a name="common-app-discovery-errors"></a>Běžné chyby zjišťování aplikací

Azure Migrate podporuje zjišťování aplikací, rolí a funkcí pomocí Azure Migrate: posouzení serveru. Zjišťování aplikací se v současné době podporuje jenom pro VMware. [Přečtěte si další informace](how-to-discover-applications.md) o požadavcích a krocích pro nastavení zjišťování aplikací.

Typické chyby zjišťování aplikací jsou shrnuté v tabulce.

**Chyba** | **Příčina** | **Akce**
--- | --- | --- | ---
10000: "nelze zjistit aplikace nainstalované na serveru". | K tomu může dojít, pokud operační systém počítače není Windows nebo Linux. | Používejte jenom zjišťování aplikací pro Windows/Linux.
10001: nelze načíst aplikace nainstalované serverem. | Vnitřní chyba – některé chybějící soubory v zařízení. | Obraťte se na podporu Microsoftu.
10002: nelze načíst aplikace nainstalované serverem. | Agent zjišťování na zařízení nemusí správně fungovat. | Pokud se problém nepodaří vyřešit během 24 hodin, obraťte se na podporu.
10003 "nelze načíst aplikace nainstalovaná na serveru". | Agent zjišťování na zařízení nemusí správně fungovat. | Pokud se problém nepodaří vyřešit během 24 hodin, obraťte se na podporu.
10004: "nepovedlo se zjistit nainstalované aplikace pro < počítače > Windows/Linux." |  Přihlašovací údaje pro přístup k < počítačům > systému Windows/Linux nejsou v zařízení k dispozici.| Přidejte přihlašovací údaje k zařízení, které má přístup k < počítačům > Windows/Linux.
10005: "nepovedlo se získat přístup k místnímu serveru". | Přístup k přihlašovacím údajům může být chybný. | Aktualizujte přihlašovací údaje zařízení a ujistěte se, že k příslušnému počítači máte přístup. 
10006: "nepovedlo se získat přístup k místnímu serveru". | K tomu může dojít, pokud operační systém počítače není Windows nebo Linux.|  Používejte jenom zjišťování aplikací pro Windows/Linux.
9000/9001/9002: "nelze zjistit aplikace nainstalované na serveru". | Nástroje VMware pravděpodobně nejsou nainstalovány nebo jsou poškozeny. | Na příslušný počítač nainstalujte nebo přeinstalujte nástroje VMware a ověřte, že je spuštěný.
9003: nelze zjistit aplikace nainstalované na serveru. | K tomu může dojít, pokud operační systém počítače není Windows nebo Linux. | Používejte jenom zjišťování aplikací pro Windows/Linux.
9004: "nelze zjistit aplikace nainstalované na serveru". | K tomu může dojít v případě, že je virtuální počítač vypnutý. | Pro zjišťování se ujistěte, že je virtuální počítač zapnutý.
9005: nepovedlo se zjistit aplikace nainstalované na virtuálním počítači. | K tomu může dojít, pokud operační systém počítače není Windows nebo Linux. | Používejte jenom zjišťování aplikací pro Windows/Linux.
9006/9007: nelze načíst aplikace nainstalované serverem. | Agent zjišťování na zařízení nemusí správně fungovat. | Pokud se problém nepodaří vyřešit během 24 hodin, obraťte se na podporu.
9008: nelze načíst aplikace nainstalované serverem. | Může se jednat o vnitřní chybu.  | TF problém se nedokáže vyřešit během 24 hodin, obraťte se na podporu.
9009: nelze načíst aplikace nainstalované serverem. | Může dojít v případě, že nastavení řízení uživatelských účtů (UAC) systému Windows na serveru jsou omezující a znemožňuje zjišťování nainstalovaných aplikací. | Vyhledejte nastavení řízení uživatelských účtů na serveru a nakonfigurujte nastavení nástroje řízení uživatelských účtů na serveru na jednu z nižších dvou úrovní.
9010: nelze načíst aplikace nainstalované serverem. | Může se jednat o vnitřní chybu.  | TF problém se nedokáže vyřešit během 24 hodin, obraťte se na podporu.
8084: nelze zjistit aplikace z důvodu chyby VMware: <Exception from VMware> | Zařízení Azure Migrate používá rozhraní API VMware ke zjišťování aplikací. K tomuto problému může dojít, pokud se při pokusu o zjištění aplikací vyvolá výjimka vCenter Server. Zpráva o chybě z VMware se zobrazí v chybové zprávě zobrazené na portálu. | Vyhledejte zprávu v [dokumentaci k VMware](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)a proveďte opravu pomocí těchto kroků. Pokud nemůžete opravit, obraťte se na podporu Microsoftu.




## <a name="next-steps"></a>Další kroky
Nastavte zařízení pro [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md)nebo [fyzické servery](how-to-set-up-appliance-physical.md).
