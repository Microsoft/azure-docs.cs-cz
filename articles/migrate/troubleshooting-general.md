---
title: Řešení potíží s Azure Migrate | Microsoft Docs
description: Poskytuje přehled známých problémů služby Azure Migrate a také tipy pro řešení běžných chyb.
author: musa-57
ms.manager: abhemraj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: hamusa
ms.openlocfilehash: 12f8f64c051d33ac2518edbe8b937521318a9e71
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284504"
---
# <a name="troubleshoot-azure-migrate"></a>Řešení problémů s Azure Migrate

[Azure Migrate](migrate-services-overview.md) poskytuje centrum nástrojů pro účely posouzení a migrace i nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců. Tento článek vám pomůže při řešení potíží s Azure Migrate, Azure Migrate posouzení serveru a migrace Azure Migrate serveru.


## <a name="find-a-project"></a>Najít projekt

Existují [dvě verze](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) Azure Migrate.


Pokud jste vytvořili projekt Azure Migrate v aktuální verzi Azure Migrate proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com)vyhledejte **Azure Migrate**.
2. V řídicím panelu Azure Migrate > **servery**vyberte v pravém horním rohu možnost **změnit** .

    ![Přepnout na existující Azure Migrate projekt](./media/troubleshooting-general/switch-project.png)

3. Vyberte odpovídající předplatné a Azure Migrate projekt.


Pokud jste projekt vytvořili v předchozí verzi Azure Migrate, udělejte toto:

1. V [Azure Portal](https://portal.azure.com)vyhledejte **Azure Migrate**.
2. Pokud jste v řídicím panelu Azure Migrate vytvořili projekt v předchozí verzi, zobrazí se banner odkazující na starší projekty. Vyberte banner.

    ![Přístup ke stávajícím projektům](./media/troubleshooting-general/access-existing-projects.png)

3. Zkontrolujte seznam starého projektu.


## <a name="create-additional-projects"></a>Vytvořit další projekty

Vytvořte nový projekt Azure Migrate následujícím způsobem:

1. V [Azure Portal](https://portal.azure.com)vyhledejte **Azure Migrate**.
2. Na Azure Migrate řídicím panelu > **servery**vyberte v pravém horním rohu možnost **změnit** .

   ![Změnit Azure Migrate projekt](./media/troubleshooting-general/switch-project.png)

3. Chcete-li vytvořit nový projekt, vyberte **možnost klikněte sem**.

   ![Vytvořit druhý Azure Migrate projekt](./media/troubleshooting-general/create-new-project.png)

## <a name="review-supported-geographies"></a>Kontrola podporovaných geografických oblastí

Zkontrolujte podporované geografické oblasti pro [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) a [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

## <a name="delete-projectsworkspaces"></a>Odstranit projekty/pracovní prostory

Při odstraňování Azure Migrate projektů a Log Analytics pracovních prostorů mějte na paměti, že:

- Při odstranění Azure Migrate projektu se odstraní projekt *a* metadata o zjištěných počítačích.
- Pokud jste připojili Log Analytics pracovní prostor k nástroji pro vyhodnocení serveru, pracovní prostor se automaticky neodstraní.
- Stejný pracovní prostor Log Analytics lze použít pro více scénářů.
- Pokud chcete pracovní prostor Log Analytics odstranit, musíte to provést ručně.


### <a name="delete-a-project"></a>Odstranit projekt

Odstranění projektu v aktuální verzi Azure Migrate:

1. Otevřete skupinu prostředků Azure, ve které se projekt vytvořil.
2. Na stránce skupina prostředků vyberte **Zobrazit skryté typy**.
3. Vyberte projekt migrace, který chcete odstranit. Typ prostředku je Microsoft. migruje/migrateprojects a odstraní ho.

Odstranění projektu ve starší verzi Azure Migrate:

1. Otevřete skupinu prostředků Azure, ve které se projekt vytvořil.
2. Vyberte projekt migrace, který chcete odstranit. Typ prostředku je migrace projektu a jeho odstranění.


### <a name="delete-a-workspace"></a>Odstranění pracovního prostoru

Přejděte do pracovního prostoru Log Analytics připojeného k projektu.
* Pokud jste neodstranili Azure Migrate projekt, najdete odkaz na pracovní prostor v **Essentials** > **Server Assessment**.
       Pracovní prostor ![LA](./media/troubleshooting-general/loganalytics-workspace.png)

     * If you've already deleted the Azure Migrate project, select **Resource Groups** in the left pane of the Azure portal. Locate the workspace in the relevant resources group, and [follow the instructions](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) to delete it.


## <a name="error-requests-must-contain-user-identity-headers"></a>Chyba: požadavky musí obsahovat hlavičky identity uživatele.

Při vytváření projektu může tato chyba znamenat, že nemáte přístup k tenantovi Azure Active Directory (Azure AD) organizace.

- Při prvním přidání do tenanta Azure AD obdržíte e-mailovou pozvánku pro připojení ke klientovi.
- Přijměte pozvánku pro úspěšné přidání do tenanta.
    - Pokud se e-mail nezobrazuje, obraťte se na uživatele s přístupem k tenantovi a požádejte ho, aby [pozvánku poslal znovu](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users) .
    - Po přijetí e-mailu s pozvánkou otevřete ho a vyberte odkaz pro přijetí pozvánky. Pak se odhlaste ze Azure Portal a znovu se přihlaste. (obnovení prohlížeče nebude fungovat.) Pak můžete začít vytvářet projekt migrace.


## <a name="error-invalid-ovf-manifest-entry"></a>Chyba: neplatný záznam manifestu OVF

Pokud se zobrazí chyba "zadaný soubor manifestu je neplatný: Neplatná položka manifestu OVF", udělejte toto:

1. Zkontrolujte jeho hodnotu hash tak, že zkontrolujete, jestli se soubor vajíček Azure Migrate zařízení správně stáhl. [Další informace](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware) Pokud hodnota hash neodpovídá, Stáhněte si znovu soubor vajíček a spusťte nasazení znovu.
2. Pokud se nasazení stále nedaří a k nasazení souboru OVF používáte klienta VMware vSphere, zkuste ho nasadit prostřednictvím webového klienta vSphere. Pokud nasazení ještě neproběhne úspěšně, zkuste použít jiný webový prohlížeč.
3. Pokud používáte webového klienta vSphere a pokusíte se ho nasadit na vCenter Server 6,5 nebo 6,7, zkuste nasadit VAJÍČKu přímo na hostiteli ESXi:
   - Připojte se k hostiteli ESXi přímo (místo vCenter Server) pomocí webového klienta (https://<*IP adresa hostitele*>/UI).
   - V > **inventáře** **domů** vyberte **soubor** > **nasadit šablonu OVF**. Přejděte k VAJÍČKám a dokončete nasazení.
4. Pokud se nasazení stále nedaří, obraťte se na podporu Azure Migrate.

## <a name="appliance-cant-connect-to-the-internet"></a>Zařízení se nemůže připojit k Internetu.

K tomu může dojít, pokud je počítač zařízení za proxy serverem.

- Ujistěte se, že přihlašovací údaje pro autorizaci zadáte, pokud je proxy vyžaduje.
- Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, přidejte tyto adresy URL do seznamu povolených adres:

    - [Adresy URL pro vyhodnocení VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
    - [Adresy URL pro posouzení technologie Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
    - [Adresy URL pro migraci bez agentů VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
    - [Adresy URL pro migraci založené na agentech VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
    - [Adresy URL pro migraci technologie Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

- Pokud pro připojení k Internetu používáte zachycení proxy serveru, importujte certifikát proxy serveru do virtuálního počítače zařízení pomocí [těchto kroků](https://docs.microsoft.com/azure/migrate/concepts-collector).

## <a name="errordatetime-synchronization"></a>Chyba: synchronizace data a času

Chyba synchronizace data a času (802) znamená, že hodiny serveru pravděpodobně nejsou synchronizovány s aktuálním časem více než pěti minutami. Změňte čas hodin na virtuálním počítači kolektoru tak, aby odpovídal aktuálnímu času:

1. Otevřete příkazový řádek správce na virtuálním počítači.
2. Chcete-li kontrolovat časové pásmo, spusťte příkaz **W32tm/TZ**.
3. Chcete-li synchronizovat čas, spusťte příkaz **W32tm/Resync**.


## <a name="error-unabletoconnecttoserver"></a>Chyba: UnableToConnectToServer

Pokud se zobrazí tato chyba připojení, možná se nebudete moci připojit k vCenter Server *servername*. com: 9443. Podrobnosti o chybě označují, že na https://*servername*. com: 9443/SDK, který může přijmout zprávu, nenaslouchá žádný koncový bod.

- Ověřte, zda používáte nejnovější verzi zařízení. Pokud nejste, upgradujte zařízení na [nejnovější verzi](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Pokud problém pořád proběhne v nejnovější verzi, zařízení nemusí být schopné přeložit zadaný vCenter Server název, nebo je možné, že je zadaný port nesprávný. Ve výchozím nastavení se kolektor pokusí připojit k portu číslo 443, pokud není zadaný port.

    1. Ověřte z zařízení parametr *servername*. com.
    2. Pokud se krok 1 nezdařil, zkuste se připojit k serveru vCenter pomocí IP adresy.
    3. Identifikujte správné číslo portu pro připojení k vCenter Server.
    4. Ověřte, že je vCenter Server v provozu.


## <a name="error-appliance-might-not-be-registered"></a>Chyba: zařízení pravděpodobně není zaregistrováno.

- Chyba 60052: Pokud účet Azure, který se používá k registraci zařízení, nemá dostatečná oprávnění, "zařízení se nemusí úspěšně zaregistrovat do Azure Migrate projektu".
    - Ujistěte se, že uživatelský účet Azure použitý k registraci zařízení má alespoň oprávnění přispěvatele k tomuto předplatnému.
    - [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) o požadovaných rolích a oprávněních Azure.
- Chyba 60039, "zařízení se nemusí úspěšně zaregistrovat do Azure Migrate projektu" může dojít v případě, že se registrace nezdaří, protože Azure Migrate projekt, který se použil k registraci zařízení, se nepovedlo najít.
    - V Azure Portal a ověřte, zda projekt existuje ve skupině prostředků.
    - Pokud projekt neexistuje, vytvořte ve skupině prostředků nový Azure Migrate projekt a znovu zaregistrujte zařízení. [Přečtěte si, jak](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) vytvořit nový projekt.

## <a name="error-key-vault-management-operation-failed"></a>Chyba: operace správy Key Vault nebyla úspěšná.

Pokud se zobrazí chyba 60030 nebo 60031, "operace správy Azure Key Vault nebyla úspěšná", udělejte toto:
- Ujistěte se, že uživatelský účet Azure použitý k registraci zařízení má alespoň oprávnění přispěvatele k tomuto předplatnému.
- Ujistěte se, že má účet přístup k trezoru klíčů, který je zadaný v chybové zprávě, a pak zkuste operaci zopakovat.
- Pokud se problém nevyřeší, obraťte se na podporu Microsoftu.
- [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) o požadovaných rolích a oprávněních Azure.

## <a name="fix-discovery-couldnt-be-initiated"></a>Oprava: zjišťování se nepovedlo inicializovat.

Chyba 60028: zjišťování nelze iniciovat z důvodu chyby. Operace se pro zadaný seznam hostitelů nebo clusterů nezdařila. znamená to, že zjišťování nebylo možné spustit na hostitelích uvedených v této chybě z důvodu problému s přístupem nebo načítáním informací o virtuálním počítači. Zbytek hostitelů byl úspěšně přidán.

- Přidejte hostitele uvedené v této chybě znovu pomocí možnosti **Přidat hostitele** .
- Pokud dojde k chybě ověřování, přečtěte si pokyny k nápravě a opravte chyby a potom zkuste znovu **spustit možnost Uložit a spustit zjišťování** .

## <a name="fix-azure-ad-operation-failed-60025"></a>Oprava: operace Azure AD se nezdařila (60025)

Chyba 60025: operace Azure AD se nezdařila. K chybě při vytváření nebo aktualizaci aplikace Azure AD dojde v případě, že se uživatelský účet Azure, který se používá k zahájení zjišťování, liší od účtu použitého k registraci zařízení. Proveďte jednu z těchto akcí:

- Ujistěte se, že uživatelský účet, který spouští zjišťování, je stejný jako ten, který se používá k registraci zařízení.
- Zadejte Azure Active Directory oprávnění k přístupu k aplikacím pro uživatelský účet, pro který se operace zjišťování nedaří.
- Odstraňte skupinu prostředků vytvořenou dříve pro Azure Migrate projekt. Vytvořte novou skupinu prostředků a začněte znovu.
- [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) o Azure Active Directory oprávnění aplikace.


## <a name="discovered-vms-not-in-portal"></a>Zjištěné virtuální počítače, které nejsou na portálu

Pokud spustíte zjišťování, aby migrace **serveru** a **migraci serveru** zobrazovaly **probíhající zjišťování**, ale tyto virtuální počítače ještě nejsou na portálu zobrazeny, vezměte na vědomí následující informace:

- Po zahájení zjišťování ze zařízení trvá zjišťování virtuálních počítačů VMware přibližně za 15 minut a asi dvě minuty pro každého přidaného hostitele pro zjišťování virtuálních počítačů Hyper-V.
- Pokud budete i nadále sledovat **probíhající zjišťování** i po těchto čekacích obdobích, vyberte **aktualizovat** na kartě **servery** . Mělo by se zobrazit počet zjištěných serverů v rámci **posouzení serveru** a **migrace serveru**.


## <a name="deleted-vms-in-the-portal"></a>Odstraněné virtuální počítače na portálu

Pokud jste nasadili zařízení, které nepřetržitě hledá vaše místní prostředí, ale odstraněné virtuální počítače se na portálu pořád zobrazují, pamatujte na toto:  

- Data zjišťování shromážděná zařízením zabírají až 30 minut, než se na portálu projeví.
- Pokud nevidíte aktuální informace po 30 minutách, aktualizujte data pomocí následujících kroků:

    1. V > servery **Azure Migrate posouzení serveru**vyberte **Přehled**.
    2. V části **Spravovat**vyberte **Agent Health**
    3. Vyberte **aktualizovat agenta**.
    1. Počkejte na dokončení operace aktualizace. Nyní byste měli vidět aktuální informace.

## <a name="vm-information-isnt-in-the-portal"></a>Informace o virtuálním počítači nejsou na portálu.

- Data zjišťování shromážděná zařízením zabírají až 30 minut, než se na portálu projeví.
- Pokud nevidíte aktuální informace po 30 minutách, aktualizujte data pomocí následujících kroků:

    1. V > servery **Azure Migrate posouzení serveru**vyberte **Přehled**.
    2. V části **Spravovat**vyberte **Agent Health**
    3. Vyberte **aktualizovat agenta**.
    1. Počkejte na dokončení operace aktualizace. Nyní byste měli vidět aktuální informace.


## <a name="fix-cant-connect-to-host-or-cluster"></a>Oprava: nejde se připojit k hostiteli nebo clusteru.

Chyba 50004: Nelze se připojit k hostiteli nebo clusteru, protože název serveru nelze přeložit. Kód chyby WinRM: 0x803381B9 se může vyskytnout, pokud služba Azure DNS pro zařízení nemůže vyřešit zadaný název clusteru nebo hostitele.

- Pokud se tato chyba zobrazuje v clusteru, plně kvalifikovaný název domény clusteru.
- Tato chyba se může zobrazit také pro hostitele v clusteru. To znamená, že zařízení se může připojit ke clusteru, ale cluster vrátí názvy hostitelů, které nejsou plně kvalifikované názvy domén. Chcete-li tuto chybu vyřešit, aktualizujte soubor hostitelů na zařízení přidáním mapování IP adresy a názvů hostitelů:
    1. Otevřete Poznámkový blok jako správce.
    2. Otevřete soubor C:\Windows\System32\Drivers\etc\hosts.
    3. Přidejte IP adresu a název hostitele do řádku. Opakujte pro každého hostitele nebo cluster, kde se zobrazí tato chyba.
    4. Uložte a zavřete soubor hostitelů.
    5. Ověřte, jestli se zařízení může připojit k hostitelům pomocí aplikace pro správu zařízení. Po 30 minutách byste měli vidět nejnovější informace pro tyto hostitele v Azure Portal.

## <a name="application-discovery-issues"></a>Problémy se zjišťováním aplikací

Zjišťování aplikací je v současné době podporováno pouze pro virtuální počítače VMware. Podpora virtuálních počítačů Hyper-V a fyzických serverů bude v budoucnu povolená. Zjišťování aplikací vyžaduje zadání přihlašovacích údajů k virtuálnímu počítači v zařízení. Přečtěte si další informace o přístupových oprávněních potřebných pro vCenter Server a pro virtuální počítače VMware. Zjišťování může selhat z důvodu některého z následujících problémů, přečtěte si prosím doporučený postup, jak je uvedeno níže, a problém vyřešte:

**Kód chyby:** | **Zpráva** | **Možná příčina** | **Doporučená akce**
--- | --- | --- | ---
10000 | Nelze zjistit aplikace nainstalované na serveru. | K tomu může dojít v případě, že operační systém spuštěný na serveru není ani Windows ani Linux. | Zjišťování nainstalovaných aplikací je podporováno pouze pro servery se systémy Windows a Linux.
10001 | Nepovedlo se načíst aplikace nainstalovaná na serveru. | Důvodem je interní chyba, protože v zařízení chybí nějaké soubory. | Kontaktujte prosím podpora Microsoftu.
10002 | Nepovedlo se načíst aplikace nainstalovaná na serveru. | K tomu může dojít, pokud agent zjišťování v zařízení Azure Migrate nepracuje správně. | Problém by se měl automaticky vyřešit během 24 hodin. Pokud problém přetrvává, kontaktujte prosím podpora Microsoftu.
10003 | Nepovedlo se načíst aplikace nainstalovaná na serveru. | K tomu může dojít, pokud agent zjišťování nepracuje správně. | Problém by se měl automaticky vyřešit během 24 hodin. Pokud problém přetrvává, kontaktujte prosím podpora Microsoftu.
10004 | Nepovedlo se zjistit nainstalované aplikace pro < počítače > Windows/Linux. |  Přihlašovací údaje pro přístup k < počítačům > Windows/Linux nebyly v zařízení Azure Migrate k dispozici. | Přidejte prosím přihlašovací údaje do zařízení Azure Migrate, které má přístup k <m počítačům se systémem Windows/Linux >.
10005 | Nepovedlo se získat přístup k místnímu serveru. | K tomu může dojít, pokud přihlašovací údaje zadané pro počítač pro přístup k serveru nejsou správné. | Aktualizujte prosím přihlašovací údaje, které jste zadali v zařízení, a ujistěte se, že je server přístupný pomocí přihlašovacích údajů.
10006 | Nepovedlo se získat přístup k místnímu serveru. | K tomu může dojít v případě, že operační systém spuštěný na serveru není ani Windows ani Linux. | Zjišťování nainstalovaných aplikací je podporováno pouze pro servery se systémy Windows a Linux.
9000 | Nepovedlo se zjistit aplikace nainstalované na virtuálním počítači. | Nástroje VMware pravděpodobně nejsou nainstalovány nebo jsou poškozeny. | Nainstalujte nebo přeinstalujte nástroje VMware na virtuálním počítači a ověřte, jestli je spuštěný.
9001 | Nepovedlo se zjistit aplikace nainstalované na virtuálním počítači. | Nástroje VMware pravděpodobně nejsou nainstalovány nebo jsou poškozeny. | Nainstalujte nebo přeinstalujte nástroje VMware na virtuálním počítači a ověřte, jestli je spuštěný.
9002 | Nepovedlo se zjistit aplikace nainstalované na virtuálním počítači. | Nástroje VMware pravděpodobně nejsou spuštěny. | Nainstalujte nebo přeinstalujte nástroje VMware na virtuálním počítači a ověřte, jestli je spuštěný.
9003 | Nelze zjistit aplikace nainstalované na serveru. | K tomu může dojít v případě, že operační systém spuštěný na serveru není ani Windows ani Linux. | Zjišťování nainstalovaných aplikací je podporováno pouze pro servery se systémy Windows a Linux.
9004 | Nelze zjistit aplikace nainstalované na serveru. | K tomu může dojít v případě, že je virtuální počítač vypnutý. | Pokud chcete zjistit nainstalované aplikace na serveru, ujistěte se, že je virtuální počítač zapnutý.
9005 | Nepovedlo se zjistit aplikace nainstalované na virtuálním počítači. | K tomu může dojít v případě, že operační systém, který běží na virtuálním počítači, není Windows ani Linux. | Zjišťování nainstalovaných aplikací je podporováno pouze pro servery se systémy Windows a Linux.
9006 | Nepovedlo se načíst aplikace nainstalovaná na serveru. | K tomu může dojít, pokud agent zjišťování nepracuje správně. | Problém by se měl automaticky vyřešit během 24 hodin. Pokud problém přetrvává, kontaktujte prosím podpora Microsoftu.
9007 | Nepovedlo se načíst aplikace nainstalovaná na serveru. | K tomu může dojít, pokud agent zjišťování nepracuje správně. | Problém by se měl automaticky vyřešit během 24 hodin. Pokud problém přetrvává, kontaktujte prosím podpora Microsoftu.
9008 | Nepovedlo se načíst aplikace nainstalovaná na serveru. | K tomuto problému může dojít z důvodu vnitřní chyby.  | Problém by se měl automaticky vyřešit během 24 hodin. Pokud problém přetrvává, kontaktujte prosím podpora Microsoftu.
9009 | Nepovedlo se načíst aplikace nainstalovaná na serveru. | K tomuto problému může dojít v případě, že nastavení řízení uživatelských účtů (UAC) systému Windows na serveru jsou omezující a znemožňuje zjišťování nainstalovaných aplikací. | Vyhledejte nastavení řízení uživatelských účtů na serveru a nakonfigurujte nastavení nástroje řízení uživatelských účtů na serveru tak, aby bylo na jednom z nižších dvou úrovní.
9010 | Nepovedlo se načíst aplikace nainstalovaná na serveru. | K tomuto problému může dojít z důvodu vnitřní chyby.  | Problém by se měl automaticky vyřešit během 24 hodin. Pokud problém přetrvává, kontaktujte prosím podpora Microsoftu.
8084 | Kvůli chybě VMware se nepovedlo zjistit aplikace: <Exception from VMware> | Zařízení Azure Migrate používá rozhraní API VMware ke zjišťování aplikací. K tomuto problému může dojít z důvodu výjimky vyvolané vCenter Server při pokusu o zjišťování aplikací. Zpráva o chybě z VMware se zobrazí v chybové zprávě zobrazené na portálu. | Projděte si [dokumentaci k VMware](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html), vyhledejte zprávu o chybě a opravte problém podle kroků v článku věnovaném řešení potíží v článku VMware. Pokud stále nemůžete tento problém vyřešit, je třeba získat podpora Microsoftu.


## <a name="fix-assessment-readiness"></a>Oprava připravenosti na posouzení

Opravte problémy připravenosti vyhodnocování následujícím způsobem:

**Chybu** | **Opravit**
--- | ---
Nepodporovaný typ spuštění | Azure nepodporuje virtuální počítače s typem spouštění EFI. Před spuštěním migrace doporučujeme převést typ spouštění na systém BIOS. <br/><br/>K obsluze migrace takových virtuálních počítačů můžete použít migraci Azure Migrate serveru. Při migraci převede typ spouštění virtuálního počítače na systém BIOS.
Podmíněně podporované operační systémy Windows | Operační systém předal své datum ukončení podpory a pro [podporu v Azure](https://aka.ms/WSosstatement)potřebuje vlastní smlouvu o podpoře (CSA). Před migrací do Azure zvažte možnost upgradovat.
Nepodporovaný operační systém Windows | Azure podporuje jenom [vybrané verze operačního systému Windows](https://aka.ms/WSosstatement). Před migrací do Azure zvažte možnost upgradovat počítač.
Podmíněně schválené operační systémy Linux | Azure schválí jenom [vybrané verze operačního systému Linux](../virtual-machines/linux/endorsed-distros.md). Před migrací do Azure zvažte možnost upgradovat počítač.
Neschválený operační systém Linux | Počítač se může v Azure spustit, ale Azure neposkytuje žádnou podporu operačního systému. Před migrací do Azure zvažte možnost upgradovat na [schválenou verzi systému Linux](../virtual-machines/linux/endorsed-distros.md) .
Neznámý operační systém | Operační systém virtuálního počítače byl ve vCenter Server zadán jako jiný. Toto chování blokuje Azure Migrate Ověření připravenosti na Azure pro virtuální počítač. Než počítač migrujete, ujistěte se, že Azure [podporuje](https://aka.ms/azureoslist) operační systém.
Nepodporovaná bitová verze | Virtuální počítače s 32 operačním systémem se můžou spouštět v Azure, ale před migrací do Azure doporučujeme upgradovat na 64.
Vyžaduje předplatné Microsoft Visual Studio. | Počítač používá klientský operační systém Windows, který je podporovaný jenom prostřednictvím předplatného sady Visual Studio.
Pro požadovaný výkon úložiště se nenašel virtuální počítač. | Výkon úložiště (vstupně-výstupní operace za sekundu [IOPS] a propustnost) požadovaná pro tento počítač překračuje podporu virtuálních počítačů Azure. Před migrací snižte požadavky na úložiště pro daný počítač.
Pro požadovaný výkon sítě se nenašel virtuální počítač. | Výkon sítě (v/v) vyžadovaný pro tento počítač překračuje podporu virtuálních počítačů Azure. Snižte požadavky na síť pro daný počítač.
Virtuální počítač se nenašel v zadaném umístění. | Před migrací použijte jiné cílové umístění.
Jeden nebo více nevhodných disků | Jeden nebo více disků připojených k virtuálnímu počítači nesplňuje požadavky Azure. Určitého<br/><br/> Azure Migrate: posouzení serveru aktuálně nepodporuje SSD úrovně Ultra disky a posuzuje disky na základě limitů disku pro spravované disky úrovně Premium (32 TB).<br/><br/> U každého disku připojeného k virtuálnímu počítači se ujistěte, že je velikost disku < 64 TB (podporuje disky SSD úrovně Ultra).<br/><br/> Pokud ne, snižte velikost disku před migrací na Azure nebo použijte více disků v Azure a propojte [je dohromady](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) , abyste dosáhli vyšších limitů úložiště. Ujistěte se, že [disky virtuálních počítačů spravované](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)službou Azure podporují výkon (IOPS a propustnost), které potřebuje každý disk.
Jeden nebo více nevhodných síťových adaptérů. | Před migrací odeberte nepoužívané síťové adaptéry z počítače.
Počet disků překračuje limit. | Před migrací z počítače odeberte nepoužívané disky.
Velikost disku překračuje limit. | Azure Migrate: posouzení serveru aktuálně nepodporuje SSD úrovně Ultra disky a posuzuje disky na základě limitů disků Premium (32 TB).<br/><br/> Azure ale podporuje disky s velikostí až 64 TB (Podporované disky SSD úrovně Ultra). Zmenšete disky na méně než 64 TB před migrací nebo použijte více disků v Azure a propojte [je dohromady](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) , abyste dosáhli vyšších limitů úložiště.
Disk není v zadaném umístění k dispozici. | Před migrací zajistěte, aby byl disk v cílovém umístění.
Disk není pro zadanou redundanci k dispozici. | Disk by měl používat typ úložiště redundance definovaný v nastavení hodnocení (ve výchozím nastavení LRS).
Nebylo možné určit vhodnost disku z důvodu vnitřní chyby. | Zkuste vytvořit nové vyhodnocení pro skupinu.
Virtuální počítač s požadovanými jádry a pamětí se nenašel. | Azure nemohl najít vhodný typ virtuálního počítače. Před migrací Snižte velikost paměti a počtu jader místního počítače.
Kvůli vnitřní chybě se nepovedlo určit vhodnost virtuálního počítače. | Zkuste vytvořit nové vyhodnocení pro skupinu.
Kvůli vnitřní chybě se nepovedlo určit vhodnost pro jeden nebo víc disků. | Zkuste vytvořit nové vyhodnocení pro skupinu.
Kvůli vnitřní chybě se nepovedlo určit vhodnost pro jeden nebo víc síťových adaptérů. | Zkuste vytvořit nové vyhodnocení pro skupinu.

## <a name="linux-vms-are-conditionally-ready"></a>Virtuální počítače se systémem Linux jsou připravené pro přípravu.

Posouzení serveru označí virtuální počítače se systémem Linux jako podmíněně připravené z důvodu známé mezery v posuzování serveru.

- Tato mezera brání tomu, aby zjistila podverzi operačního systému Linux nainstalovaného na místních virtuálních počítačích.
- Například pro RHEL 6,10 aktuálně vyhodnocování serveru detekuje jako verzi operačního systému pouze RHEL 6.
-  Vzhledem k tomu, že Azure schválí pouze konkrétní verze systému Linux, jsou virtuální počítače se systémem Linux v současnosti označeny jako podmíněně připravené v rámci posouzení serveru.
- Můžete zjistit, jestli operační systém Linux běžící na místním virtuálním počítači je v Azure schválený, a to kontrolou [podpory Azure Linux](https://aka.ms/migrate/selfhost/azureendorseddistros).
-  Po ověření schválené distribuce můžete toto upozornění ignorovat.

## <a name="azure-skus-exceed-on-premises-sizing"></a>Skladové jednotky Azure překračují místní velikost

Posouzení serveru Azure Migrate může doporučit skladové položky Azure s více jádry a pamětí než aktuální místní přidělení na základě typu posouzení:


- Doporučení SKU virtuálního počítače závisí na vlastnostech posouzení.
- To je ovlivněno typem posouzení, které provádíte při posuzování serveru: na *základě výkonu*nebo *jako v*místním prostředí.
- U posouzení na základě výkonu považuje server hodnocení za využití dat z místních virtuálních počítačů (CPU, paměti, disku a sítě) k určení správné cílové SKU virtuálního počítače pro vaše místní virtuální počítače. Také přináší faktor pohodlí při určování efektivního využití.
- Pro místní velikost se data o výkonu neberou v úvahu a cílová skladová jednotka se doporučuje na základě místního přidělení.

Pokud chcete vidět, jak to může ovlivnit doporučení, Podívejme se na příklad:

Máme místní virtuální počítač se čtyřmi jádry a 8 GB paměti s 50% využitím procesoru a 50% využití paměti a stanovený faktor komfortu 1,3.

-  Pokud je posouzení **místní**, doporučuje se skladová položka virtuálního počítače Azure se 4 jádry a 8 GB paměti.
- Pokud je hodnocení založené na výkonu, vychází z efektivního využití procesoru a paměti (50% ze 4 jader × 1,3 = 2,6 jader a 50% z 8 GB paměti * 1,3 = 5,3-GB paměti), SKU nejlevnější virtuálních počítačů se čtyřmi jádry (nejbližší podporovaný počet jader) a osmi GB paměti (s nejbližším podporovaným velikost paměti) se doporučuje.
- [Přečtěte si další informace](concepts-assessment-calculation.md#sizing) o velikosti hodnocení.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>Diskové jednotky Azure větší než místní

Posouzení serveru Azure Migrate může doporučit větší disk na základě typu posouzení.
- Velikost disku v posuzování serveru závisí na dvou vlastnostech posouzení: kritéria změny velikosti a typ úložiště.
- Pokud jsou kritéria změny velikosti **založená na výkonu**a typ úložiště je nastaven na hodnotu **automaticky**, při určení typu cílového disku (HDD úrovně Standard, SSD úrovně Standard nebo Premium) se posuzují hodnoty IOPS a propustnosti disku. Pak se doporučuje disková jednotka disku z typu disku a doporučení posuzuje požadavky na velikost místního disku.
- Pokud jsou kritéria změny velikosti v **závislosti na výkonu**a typ úložiště je **Premium**, doporučuje se SKU na disku Premium v Azure na základě požadavků na vstupně-výstupní operace, propustnost a velikost na místním disku. Stejná logika se používá k provedení velikosti disku, pokud jsou kritéria změny velikosti **místní** a typ úložiště je **HDD úrovně Standard**, **SSD úrovně Standard**nebo **Premium**.

Příklad: Pokud máte místní disk s 32 GB paměti, ale agregované vstupně-výstupní operace čtení a zápisu pro disk je 800 IOPS, vyhodnocování serveru doporučuje prémiový disk (kvůli vyššímu počtu požadavků IOPS) a potom doporučuje SKU disku, které může podporovat r. ezdrátovou IOPS a velikost. Nejbližší shodou by v tomto případě byla skladová položka P15 (256 GB, 1 100 IOPS). I když velikost požadovaná místním diskem byla 32 GB, vyhodnocování serveru doporučuje větší disk kvůli vysokému počtu požadavků na požadavky na místní disk.

## <a name="fix-percentage-of-utilized-core-or-memory-missing"></a>Oprava: chybějící procento využitého jádra nebo paměti

Sestavy posouzení serveru "PercentageOfCoresUtilizedMissing" nebo "PercentageOfMemoryUtilizedMissing", když Azure Migrate zařízení nemůžou shromažďovat údaje o výkonu pro příslušné místní virtuální počítače.

- Tato situace může nastat, pokud jsou virtuální počítače vypnuté během doby hodnocení. Zařízení nemůže shromažďovat údaje o výkonu pro virtuální počítač, když je vypnutý.
- Pokud nechybí pouze čítače paměti a snažíte se vyhodnotit virtuální počítače Hyper-V, ověřte, zda je na těchto virtuálních počítačích povolena dynamická paměť. K dispozici je známý problém jenom pro virtuální počítače Hyper-V, ve kterém Azure Migrate zařízení nemůže shromažďovat data o využití paměti pro virtuální počítače, které nemají povolenou dynamickou paměť.
- Pokud některý z čítačů výkonu chybí, Azure Migrate posouzení serveru se vrátí k přiděleným jádrům a paměti a doporučí odpovídající velikost virtuálního počítače.

## <a name="is-the-vm-os-license-cost-included-in-cost-assessment"></a>Jsou náklady na licenci na operační systém virtuálních počítačů zahrnuté do odhadu nákladů?

Posouzení Azure Migrate serveru v současné době zohledňuje náklady na licenci operačního systému jenom pro počítače s Windows. Náklady na licenci pro počítače se systémem Linux se momentálně nepovažují za.

## <a name="performance-history-and-percentile-use"></a>Využití historie výkonu a percentilu

Tyto vlastnosti se vztahují pouze na výkon při vyhodnocování Azure Migrate serveru.

Nástroj Hodnocení serverů průběžně shromažďuje data o výkonu místních počítačů a používá je k doporučení skladové položky virtuálního počítače a disku v Azure. Tato data o výkonu shromažďuje vyhodnocení serveru následujícím způsobem:
- Zařízení Azure Migrate neustále profiluje místní prostředí za účelem shromažďování dat o využití v reálném čase každých 20 sekund pro virtuální počítače VMware a každých 30 sekund pro virtuální počítače Hyper-V.
- Zařízení shrnuje vzorek o 20 nebo 30 sekundách, který vytvoří jeden datový bod každých 10 minut. Pokud chcete vytvořit jeden datový bod, zařízení vybere nejvyšší hodnotu ze všech ukázek 20 sekund a 30 sekund a pak je pošle do Azure.
- Když v nástroji Hodnocení serverů vytvoříte posouzení, na základě doby výkonu a hodnoty percentilu historie výkonu se zjistí reprezentativní hodnota efektivního využití. Například pokud je historie výkonu jeden týden a procento využití je 95., Azure Migrate seřadí všechny 10 minutové ukázkové body za poslední týden ve vzestupném pořadí a pak jako reprezentativní hodnotu vybere 95. percentil.
- Hodnota 95. percentilu zajišťuje, že budete ignorovat jakékoli odlehlé hodnoty, které mohou být zahrnuty, pokud vyberete 99 percentil.
- Pokud chcete vybrat špičku pro období a nechcete vyznačit žádné z nich, měli byste vybrat 99 percentil pro využití percentilu.


## <a name="i-cant-find-dependency-visualization-for-azure-government"></a>Nemohu najít vizualizaci závislostí pro Azure Government

Azure Migrate závisí na Service Map funkce Vizualizace závislostí. Protože Service Map v tuto chvíli není v Azure Government k dispozici, tato funkce není v Azure Government dostupná.

## <a name="dependencies-dont-show-after-installing-agents"></a>Po instalaci agentů se nezobrazují závislosti

Po instalaci agentů Vizualizace závislostí na místní virtuální počítače Azure Migrate k zobrazení závislostí na portálu obvykle trvá 15-30 minut. Pokud jste čekali na více než 30 minut, ujistěte se, že Microsoft Monitoring Agent (MMA) se může připojit k pracovnímu prostoru Log Analytics.

Pro virtuální počítače s Windows:
1. V Ovládacích panelech spusťte MMA.
2. Ve **vlastnostech Microsoft Monitoring Agent** > **Azure Log Analytics (OMS)** zkontrolujte, že je **stav** pracovního prostoru zelený.
3. Pokud stav není zelený, zkuste odebrat pracovní prostor a znovu ho přidat do MMA.

      ![Dialogové okno Vlastnosti MMA](./media/troubleshooting-general/mma-status.png)

Pro virtuální počítače se systémem Linux se ujistěte, že instalační příkazy pro MMA a agenta závislostí byly úspěšné.

## <a name="supported-mma-os"></a>Podporovaný operační systém MMA

 Zkontrolujte podporované operační systémy [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)a [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) .

## <a name="supported-dependency-agent-os"></a>Podporovaný operační systém agenta závislosti

Zkontrolujte podporované operační systémy [Windows a Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) .

## <a name="dependencies-for-more-than-an-hour"></a>Závislosti po dobu delší než hodinu

I když Azure Migrate umožňuje přejít zpět k určitému datu za poslední měsíc, maximální doba, po kterou můžete vizualizovat závislosti, je jedna hodina.

Například můžete použít funkci časového trvání na mapě závislostí k zobrazení závislostí včera, ale můžete je zobrazit pouze v jednom hodinovém období.

Můžete ale použít protokoly Azure Monitor k [dotazování dat závislosti](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) v delší době trvání.

## <a name="i-cant-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Nejde vizualizovat závislosti pro skupiny s více než 10 virtuálními počítači.

V Azure Migrate hodnocení serveru můžete [vizualizovat závislosti pro skupiny](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) s až 10 virtuálními počítači. U větších skupin doporučujeme, abyste virtuální počítače rozdělili do menších skupin pro vizualizaci závislostí.

## <a name="machines-show-install-agent-not-view-dependencies"></a>Počítače ukazují, že "nainstalovat agenta" nejsou "Zobrazit závislosti"

Po migraci počítačů s povolenou vizualizací závislostí do Azure se můžou počítače místo příkazu "Zobrazit závislosti" Zobrazit akce "instalovat agenta", a to kvůli následujícímu chování:


- Po migraci do Azure jsou místní počítače vypnuté a s ekvivalentními virtuálními počítači se prosazuje v Azure. Tyto počítače získají jinou adresu MAC.
- Počítače mohou mít také jinou IP adresu, a to na základě toho, zda jste zachovali místní IP adresu nebo ne.
- Pokud se adresy MAC i IP liší od místních, Azure Migrate nepřidruží místní počítače k žádným Service Mapm datům závislostí. V takovém případě se zobrazí možnost nainstalovat agenta místo zobrazení závislostí.
- Po otestování migrace do Azure zůstanou místní počítače zapnuté podle očekávání. Ekvivalentní počítače propracované v Azure získají jinou adresu MAC a můžou získat různé IP adresy. Pokud neblokujete odchozí Azure Monitor přenosů protokolů z těchto počítačů, Azure Migrate nebude přidružit místní počítače k žádným Service Map datům závislostí, a proto se zobrazí možnost nainstalovat agenty, nikoli zobrazit závislosti.


## <a name="collect-network-traffic-logs-in-portal"></a>Shromažďovat protokoly síťového provozu na portálu

Shromážděte protokoly následujícím způsobem:

1. Přihlásit se na [Azure Portal](https://portal.azure.com).
2. Stisknutím klávesy F12 spusťte Vývojářské nástroje. V případě potřeby zrušte zaškrtnutí **políčka vymazat položky u nastavení navigace** .
3. Vyberte kartu **síť** a zahajte zachytávání síťového provozu:
   - V části Chrome vyberte **zachovat protokol**. Záznam by se měl spustit automaticky. Červený kroužek indikuje, že se zaznamenává provoz. Pokud se červené kolečko nezobrazí, vyberte černý kroužek, který chcete spustit.
   - V aplikaci Microsoft Edge a Internet Explorer by se měl záznam spustit automaticky. Pokud tomu tak není, vyberte zelené tlačítko Přehrát.
4. Zkuste reprodukování chyby.
5. Po zjištění chyby při nahrávání, zastavení záznamu a uložení kopie zaznamenané aktivity:
   - V Chrome klikněte pravým tlačítkem a vyberte **Uložit jako Har s obsahem**. Tato akce zkomprimuje a exportuje protokoly jako soubor. har.
   - V Microsoft Edge nebo Internet Exploreru vyberte možnost **exportovat zachycené přenosy** . Tato akce komprimuje a exportuje protokol.
6. Vyberte kartu **Konzola** a vyhledejte všechna upozornění a chyby. Uložení protokolu konzoly:
   - V prohlížeči Chrome klikněte pravým tlačítkem myši kdekoli v protokolu konzoly. Vyberte **Uložit jako**, exportovat a protokol zip.
   - V Microsoft Edge nebo Internet Exploreru klikněte pravým tlačítkem na chyby a vyberte **Kopírovat vše**.
7. Zavřete Vývojářské nástroje.
