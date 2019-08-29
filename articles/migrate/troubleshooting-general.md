---
title: Řešení potíží s Azure Migrate | Microsoft Docs
description: Poskytuje přehled známých problémů služby Azure Migrate a také tipy pro řešení běžných chyb.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: 7fea6d16c8846909a8ce9bb33aae74ce343018fa
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142325"
---
# <a name="troubleshoot-azure-migrate"></a>Řešení problémů s Azure Migrate

[Azure Migrate](migrate-services-overview.md) poskytuje centrum nástrojů pro účely posouzení a migrace i nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců. Tento článek vám pomůže při odstraňování chyb s Azure Migrate, Azure Migrate posouzení serveru a migrace Azure Migrate serveru.

## <a name="azure-migrate-project-issues"></a>Problémy s Azure Migrate projektu

### <a name="i-cant-find-my-existing-azure-migrate-project"></a>Nemohu najít stávající projekt Azure Migrate.

Existují [dvě verze](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) Azure Migrate. V závislosti na verzi, ve které jste projekt vytvořili, použijte jednu z následujících metod:

* Pokud hledáte projekt, který byl vytvořen s předchozí verzí (starým prostředím) Azure Migrate, použijte následující postup k vyhledání projektu:

    1. Přejít na [Azure Portal](https://portal.azure.com)a vyhledat **Azure Migrate**.
    2. Na řídicím panelu Azure Migrate se zobrazí banner, který zmiňuje přístup ke starším projektům. Tento banner se zobrazí pouze v případě, že jste vytvořili projekt se starým prostředím. Vyberte banner.

       ![Přístup ke stávajícím projektům](./media/troubleshooting-general/access-existing-projects.png)

    3. Nyní se zobrazí seznam existujících projektů, které byly vytvořeny v předchozí verzi Azure Migrate.

* Pokud hledáte projekt, který byl vytvořen s aktuální verzí (nové prostředí), použijte následující postup pro vyhledání projektu:

    1. Přejít na [Azure Portal](https://portal.azure.com)a vyhledat **Azure Migrate**.
    2. Na řídicím panelu Azure Migrate přejděte na stránku **servery** v levém podokně a v pravém horním rohu vyberte **změnit** .

       ![Přepnout na existující Azure Migrate projekt](./media/troubleshooting-general/switch-project.png)

    3. Vyberte odpovídající předplatné a Azure Migrate projekt.

### <a name="how-do-i-create-a-second-azure-migrate-project"></a>Návody vytvořit druhý Azure Migrate projekt?

Pomocí těchto kroků vytvořte nový projekt Azure Migrate:

1. Přejít na [Azure Portal](https://portal.azure.com)a vyhledat **Azure Migrate**.
2. Na řídicím panelu Azure Migrate přejděte na stránku **servery** v levém podokně a v pravém horním rohu vyberte **změnit** .

   ![Změnit Azure Migrate projekt](./media/troubleshooting-general/switch-project.png)

3. Chcete-li vytvořit nový projekt, vyberte **možnost klikněte sem**.

   ![Vytvořit druhý Azure Migrate projekt](./media/troubleshooting-general/create-new-project.png)

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Které geografické oblasti Azure Azure Migrate podporují?

Podívejte se na seznamy pro [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) a [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="how-do-i-delete-azure-migrate-projects-and-associated-log-analytics-workspaces"></a>Návody odstranit Azure Migrate projekty a přidružené Log Analytics pracovní prostory?

Při odstranění Azure Migrate projektu se odstraní projekt migrace *a* metadata o zjištěných počítačích. Pokud jste ale k nástroji pro vyhodnocení serveru připojili Log Analytics pracovní prostor, Log Analytics pracovní prostor se automaticky neodstraní. (Stejný Log Analytics pracovní prostor lze použít pro více případů použití.) Pokud chcete také odstranit Log Analytics pracovní prostor, musíte to provést ručně:

1. Přejděte do pracovního prostoru Log Analytics připojeného k projektu.
     * Pokud jste ještě neodstranili projekt migrace, můžete najít odkaz na pracovní prostor na stránce Přehled posouzení serveru v části základy.

       ![Pracovní prostor LA](./media/troubleshooting-general/loganalytics-workspace.png)

     * Pokud jste už projekt migrace odstranili, v levém podokně Azure Portal vyberte **skupiny prostředků** . Přejděte do skupiny prostředků, ve které se pracovní prostor vytvořil, a pak ho vyhledejte.
2. Postupujte podle pokynů v části [odstranění pracovního prostoru Azure Log Analytics pomocí Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace).

### <a name="migration-project-creation-failed-with-a-requests-must-contain-user-identity-headers-error"></a>Nepovedlo se vytvořit projekt migrace se chybou "požadavky musí obsahovat hlavičky identity uživatele".

K tomuto problému může dojít pro uživatele, kteří nemají přístup k tenantovi Azure Active Directory (Azure AD) organizace. Při prvním přidání uživatele do tenanta služby Azure AD obdrží uživatel e-mailové pozvání, aby se připojil ke klientovi. Uživatelé musí přijmout pozvánku, aby se úspěšně přidaly do tenanta. Pokud se e-mail nezobrazuje, obraťte se na uživatele, který už má k tenantovi přístup, a požádejte ho, aby pozvánku poslal znovu, a to pomocí kroků uvedených v tématu [opětovné odeslání pozvánky uživatelům typu Host](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Po přijetí e-mailu s pozvánkou musíte otevřít e-mail a vybrat odkaz pro přijetí pozvánky. Pak se musíte odhlásit z Azure Portal a znovu se přihlásit. (Aktualizace prohlížeče nebude fungovat.) Pak můžete začít vytvářet projekt migrace.

## <a name="appliance-issues"></a>Problémy se zařízením

### <a name="deployment-of-an-azure-migrate-appliance-for-vmware-failed-with-a-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry-error"></a>Nasazení zařízení Azure Migrate pro VMware se nepovedlo, protože zadaný soubor manifestu je neplatný: Neplatná položka manifestu OVF – chyba.

1. Zkontrolujte jeho hodnotu hash tak, že zkontrolujete, jestli se soubor vajíček Azure Migrate zařízení správně stáhl. Pokyny najdete v tématu [Příprava virtuálních počítačů VMware pro posouzení a migraci do Azure](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Pokud hodnota hash neodpovídá, Stáhněte si znovu soubor vajíček a spusťte nasazení znovu.
2. Pokud nasazení stále selhává a pokud používáte klienta VMware vSphere k nasazení souboru OVF, zkuste ho nasadit prostřednictvím webového klienta vSphere. Pokud nasazení ještě neproběhne úspěšně, zkuste použít jiný webový prohlížeč.
3. Pokud používáte webového klienta vSphere a pokusíte se ho nasadit na vCenter Server 6,5 nebo 6,7, zkuste nasadit VAJÍČKu přímo na hostiteli ESXi pomocí následujících kroků:
   - Připojte se k hostiteli ESXi přímo (místo vCenter Server) pomocí webového klienta (https://<*IP adresa hostitele*>/UI).
   - Přejít na inventáře **Domů** > 
   - Vyberte šablonu **soubor** > **nasazení OVF**. Přejděte k VAJÍČKám a dokončete nasazení.
4. Pokud nasazení ještě neproběhne úspěšně, obraťte se na podporu Azure Migrate.

### <a name="the-appliance-cant-connect-to-the-internet"></a>Zařízení se nemůže připojit k Internetu.

K tomuto chování může dojít, když je počítač, který používáte, za proxy serverem. Ujistěte se, že přihlašovací údaje pro autorizaci zadáte, pokud je proxy vyžaduje.
Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, nezapomeňte přidat tyto požadované adresy URL do seznamu povolených adres:

- [Posouzení serveru pro VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
- [Posouzení serveru pro Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
- [Migrace serveru pro VMware (bez agentů)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
- [Migrace serveru pro VMware (založená na agentovi)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
- [Migrace serveru pro Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

Pokud pro připojení k Internetu používáte zachycení proxy serveru, musíte importovat certifikát proxy do virtuálního počítače zařízení. Certifikát proxy můžete importovat podle kroků v části [Azure Migrate zařízení](https://docs.microsoft.com/azure/migrate/concepts-collector).

### <a name="error-802-date-and-time-synchronization-error"></a>Chyba 802: Chyba synchronizace data a času.

Hodiny serveru pravděpodobně nebudou synchronizovány s aktuálním časem více než pět minut. Změňte čas času na virtuálním počítači kolektoru tak, aby odpovídal aktuálnímu času, a to následujícím způsobem:

1. Otevřete příkazový řádek správce na virtuálním počítači.
2. Chcete-li kontrolovat časové pásmo, spusťte příkaz **W32tm/TZ**.
3. Chcete-li synchronizovat čas, spusťte příkaz **W32tm/Resync**.


###  <a name="connection-failed-error-unabletoconnecttoserver"></a>Připojení se nezdařilo. Chyba: UnableToConnectToServer.

Možná se nemůžete připojit k vCenter Server *servername*. com: 9443. Podrobnosti o chybě označují, že v https://*servername*. com neexistuje žádný koncový bod, který by mohl zprávu přijmout.

V takovém případě Ověřte, zda používáte nejnovější verzi zařízení sběrače. Pokud nejste, upgradujte zařízení na [nejnovější verzi](https://docs.microsoft.com/azure/migrate/concepts-collector).

Pokud problém stále probíhá v nejnovější verzi, počítač kolektoru možná nebude schopen přeložit zadaný vCenter Server název, nebo je možné, že je zadaný port nesprávný. Ve výchozím nastavení se kolektor pokusí připojit k portu číslo 443, pokud není zadaný port.

1. Z počítače kolektoru použijte parametr *servername*. com.
2. Pokud se krok 1 nezdařil, zkuste se připojit k serveru vCenter pomocí IP adresy.
3. Zjistěte správné číslo portu pro připojení k serveru vCenter.
4. Ověřte, zda je Server vCenter v provozu.


### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60052"></a>Zařízení se nemusí úspěšně zaregistrovat do projektu Azure Migrate (ID chyby: 60052).

K této chybě dochází, pokud účet Azure, který se používá k registraci zařízení, nemá dostatečná oprávnění. Ujistěte se, že uživatelský účet Azure použitý k registraci zařízení má alespoň oprávnění přispěvatele k tomuto předplatnému. [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) o požadovaných rolích a oprávněních Azure.

### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60039"></a>Zařízení se nemusí úspěšně zaregistrovat do projektu Azure Migrate (ID chyby: 60039).

Nepovedlo se najít projekt Azure Migrate vybraný k registraci zařízení. V této situaci se registrace nezdařila. Otevřete Azure Portal a ověřte, jestli projekt ve vaší skupině prostředků existuje. Pokud projekt neexistuje, vytvořte ve skupině prostředků nový Azure Migrate projekt a znovu zaregistrujte zařízení. [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) o vytvoření nového projektu Azure Migrate.

### <a name="an-azure-key-vault-management-operation-failed-error-id-60030-60031"></a>Operace správy Azure Key Vault se nezdařila (ID chyby: 60030, 60031).

Ujistěte se, že uživatelský účet Azure použitý k registraci zařízení má alespoň oprávnění přispěvatele k tomuto předplatnému. Také se ujistěte, že má účet přístup k trezoru klíčů, který je uvedený v chybové zprávě, a pak zkuste operaci zopakovat. Pokud se problém nevyřeší, obraťte se na podporu Microsoftu. [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) o požadovaných rolích a oprávněních Azure.

### <a name="discovery-couldnt-be-initiated-because-of-an-error-the-operation-failed-for-the-specified-list-of-hosts-or-clusters-error-id-60028"></a>Zjišťování se nepodařilo iniciovat z důvodu chyby. Operace se pro zadaný seznam hostitelů nebo clusterů nezdařila (ID chyby: 60028).

Na hostitelích uvedených v chybě se nepodařilo spustit zjišťování, protože došlo k problému při přístupu nebo načítání informací o virtuálním počítači. Zbytek hostitelů byl úspěšně přidán. Přidejte hostitele uvedené v této chybě znovu pomocí možnosti **Přidat hostitele** . Pokud dojde k chybě ověřování, přečtěte si pokyny k nápravě a opravte chyby a potom zkuste znovu **spustit možnost Uložit a spustit zjišťování** .

### <a name="an-azure-ad-operation-failed-the-error-occurred-while-creating-or-updating-the-azure-ad-application-error-id-60025"></a>Operace Azure AD se nezdařila. Při vytváření nebo aktualizaci aplikace Azure AD došlo k chybě (ID chyby: 60025).

K této chybě dojde v případě, že je uživatelský účet Azure použitý k zahájení zjišťování jiný než účet použitý k registraci zařízení. Můžete provést jednu z následujících akcí:
1. Ujistěte se, že uživatelský účet, který spouští zjišťování, je stejný jako ten, který se používá k registraci zařízení.
1. Poskytněte přístupová oprávnění k aplikaci AAD k druhému uživatelskému účtu, pro který se operace zjišťování nedaří.
1. Odstraňte skupinu prostředků dříve vytvořenou pro Azure Migrate projektu a vytvořte další skupinu prostředků, kterou chcete spustit znovu.

[Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) o oprávněních aplikace AAD.

## <a name="discovery-issues"></a>Problémy se zjišťováním

### <a name="i-started-discovery-but-dont-see-the-discovered-vms-on-the-azure-portal-the-server-assessment-and-server-migration-tiles-show-a-status-of-discovery-in-progress"></a>Zahájil (a) jsem zjišťování, ale na Azure Portal se nezobrazily zjištěné virtuální počítače. Dlaždice **Server Assessment** a **migrace serveru** zobrazují stav "Probíhá zjišťování".
Po zahájení zjišťování ze zařízení počkejte určitou dobu, než se zjištěné počítače zobrazí na Azure Portal. Zjišťování VMware trvá přibližně 15 minut a přibližně 2 minuty na přidané hostitele pro zjišťování technologie Hyper-V. Pokud se vám nadále zobrazuje "zjišťování probíhá", a to i po těchto čekacích obdobích, vyberte **aktualizovat** na kartě **servery** . Mělo by se zobrazit počet zjištěných serverů na dlaždicích Server pro vyhodnocení a migraci serveru.


### <a name="im-using-an-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-shown-in-the-portal"></a>Používám zařízení, které nepřetržitě zjišťuje místní prostředí, ale virtuální počítače, které jsou v místním prostředí odstraněny, se pořád zobrazují na portálu.

Data zjišťování shromážděná zařízením zabírají až 30 minut, než se na portálu projeví. Pokud nevidíte informace o aktuálním stavu ani po 30 minutách, aktualizujte data pomocí následujících kroků:

1. Přejít na **servery** > **Azure Migrate posouzení serveru**a vyberte **Přehled**.
2. V části **Spravovat**vyberte **Agent Health**
3. Vyberte **aktualizovat agenta**. Tato možnost se zobrazí pod seznamem agentů.
4. Počkejte na dokončení operace aktualizace. Teď byste měli vidět aktuální informace na vašich virtuálních počítačích.

### <a name="i-dont-see-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>Nevidím na portálu nejnovější informace o místních virtuálních počítačích.

Aby se data zjišťování shromážděná zařízením mohla projevit na portálu, trvá až 30 minut. Pokud nevidíte informace o aktuálním stavu ani po 30 minutách, aktualizujte data pomocí následujících kroků:

1. Přejít na **servery** > **Azure Migrate posouzení serveru**a vyberte **Přehled**.
2. V části **Spravovat**vyberte **Agent Health**
3. Vyberte **aktualizovat agenta**. Tato možnost se zobrazí pod seznamem agentů.
4. Počkejte na dokončení operace aktualizace. Teď byste měli vidět aktuální informace na vašich virtuálních počítačích.

### <a name="cant-connect-to-a-host-or-cluster-because-the-server-name-cant-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>Nelze se připojit k hostiteli nebo clusteru, protože název serveru nelze přeložit. Kód chyby WinRM: 0x803381B9 (ID chyby: 50004).
K této chybě dojde, pokud služba Azure DNS pro zařízení nemůže vyřešit zadaný název clusteru nebo hostitele. Pokud se tato chyba zobrazuje v clusteru, zkuste zadat plně kvalifikovaný název domény (FQDN) clusteru.

Tato chyba se může zobrazit také pro hostitele v clusteru. V takovém případě se zařízení může připojit ke clusteru, ale cluster vrací názvy hostitelů bez plně kvalifikovaného názvu domény.

Chcete-li tuto chybu vyřešit, aktualizujte soubor hostitelů na zařízení přidáním mapování IP adresy a názvů hostitelů:
1. Otevřete Poznámkový blok jako správce. Pak otevřete soubor C:\Windows\System32\Drivers\etc\hosts.
2. Přidejte IP adresu a název hostitele do řádku. Opakujte pro každého hostitele nebo cluster, kde se zobrazí tato chyba.
3. Uložte a zavřete soubor hostitelů.
4. Můžete ověřit, jestli se zařízení může připojit k hostitelům pomocí aplikace pro správu zařízení. Po 30 minutách byste si měli být schopni zobrazit nejnovější informace pro tyto hostitele na Azure Portal.


## <a name="assessment-issues"></a>Problémy posouzení

### <a name="azure-readiness-issues"></a>Problémy s připraveností na Azure

Problém | Náprava
--- | ---
Nepodporovaný typ spuštění | Azure nepodporuje virtuální počítače s typem spouštění EFI. Před spuštěním migrace doporučujeme převést typ spouštění na systém BIOS. <br/><br/>K obsluze migrace takových virtuálních počítačů můžete použít migraci Azure Migrate serveru. Při migraci převede typ spouštění virtuálního počítače na systém BIOS.
Podmíněně podporované operační systémy Windows | Operační systém předal své datum ukončení podpory a potřebuje vlastní smlouvu o podpoře (CSA) pro [podporu v Azure](https://aka.ms/WSosstatement). Před migrací do Azure zvažte možnost upgradovat operační systém.
Nepodporovaný operační systém Windows | Azure podporuje jenom [vybrané verze operačního systému Windows](https://aka.ms/WSosstatement). Před migrací do Azure zvažte možnost upgradovat operační systém počítače.
Podmíněně schválený operační systém Linux | Azure schválí jenom [vybrané verze operačního systému Linux](../virtual-machines/linux/endorsed-distros.md). Před migrací do Azure zvažte možnost upgradovat operační systém počítače.
Neschválený operační systém Linux | Počítač se může v Azure spustit, ale Azure neposkytuje žádnou podporu operačního systému. Před migrací do Azure zvažte možnost upgradovat operační systém na [schválenou verzi systému Linux](../virtual-machines/linux/endorsed-distros.md) .
Neznámý operační systém | Operační systém virtuálního počítače byl ve vCenter Server zadán jako jiný. Toto chování blokuje Azure Migrate Ověření připravenosti na Azure pro virtuální počítač. Před migrací počítače se ujistěte, že Azure [podporuje](https://aka.ms/azureoslist) operační systém počítače.
Nepodporovaná bitová verze operačního systému | Virtuální počítače s 32 operačním systémem se můžou spouštět v Azure, ale před migrací do Azure doporučujeme upgradovat operační systém virtuálního počítače na 64.
Vyžaduje předplatné Microsoft Visual Studio. | Počítač používá klientský operační systém Windows, který je podporovaný jenom prostřednictvím předplatného sady Visual Studio.
Pro požadovaný výkon úložiště se nenašel virtuální počítač. | Výkon úložiště (vstupně-výstupní operace za sekundu [IOPS] a propustnost) požadovaná pro tento počítač překračuje podporu virtuálních počítačů Azure. Před migrací snižte požadavky na úložiště pro daný počítač.
Pro požadovaný výkon sítě se nenašel virtuální počítač. | Výkon sítě (v/v) vyžadovaný pro tento počítač překračuje podporu virtuálních počítačů Azure. Snižte požadavky na síť pro daný počítač.
Virtuální počítač se nenašel v zadaném umístění. | Před migrací použijte jiné cílové umístění.
Minimálně jeden nevhodný disk | Jeden nebo více disků připojených k virtuálnímu počítači nesplňuje požadavky Azure. Azure Migrate: Posouzení serveru v současné době nepodporuje SSD úrovně Ultra disky a posuzuje disky na základě omezení disku pro spravované disky úrovně Premium (32 TB).  U každého disku připojeného k virtuálnímu počítači se ujistěte, že je velikost disku < 64 TB (podporuje disky SSD úrovně Ultra), pokud ne, zmenšete velikost disku před migrací do Azure nebo použitím více disků v Azure a prodávejte [je dohromady](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) , aby se dosáhlo vyšších limitů úložiště. Ujistěte se, že [disky virtuálních počítačů spravované](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)službou Azure podporují výkon (IOPS a propustnost), které potřebuje každý disk.
Jeden nebo více nevhodných síťových adaptérů. | Před migrací odeberte nepoužívané síťové adaptéry z počítače.
Počet disků překračuje limit. | Před migrací z počítače odeberte nepoužívané disky.
Velikost disku překračuje limit. | Azure Migrate: Posouzení serveru v současné době nepodporuje SSD úrovně Ultra disky a posuzuje disky na základě limitů disků Premium (32 TB). Azure ale podporuje disky s velikostí až 64 TB (Podporované disky SSD úrovně Ultra). Zmenšete disky na méně než 64 TB před migrací nebo použijte více disků v Azure a propojte [je dohromady](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) , abyste dosáhli vyšších limitů úložiště.
Disk není v zadaném umístění k dispozici. | Před migrací zajistěte, aby byl disk v cílovém umístění.
Disk není pro zadanou redundanci k dispozici. | Disk by měl používat typ úložiště redundance definovaný v nastavení hodnocení (ve výchozím nastavení LRS).
Nebylo možné určit vhodnost disku z důvodu vnitřní chyby. | Zkuste vytvořit nové vyhodnocení pro skupinu.
Virtuální počítač s požadovanými jádry a pamětí se nenašel. | Azure nemohl najít vhodný typ virtuálního počítače. Před migrací Snižte velikost paměti a počtu jader místního počítače.
Kvůli vnitřní chybě se nepovedlo určit vhodnost virtuálního počítače. | Zkuste vytvořit nové vyhodnocení pro skupinu.
Kvůli vnitřní chybě se nepovedlo určit vhodnost pro jeden nebo víc disků. | Zkuste vytvořit nové vyhodnocení pro skupinu.
Kvůli vnitřní chybě se nepovedlo určit vhodnost pro jeden nebo víc síťových adaptérů. | Zkuste vytvořit nové vyhodnocení pro skupinu.

### <a name="i-cant-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>Nemohu ve vlastnostech posouzení zadat smlouva Enterprise (EA) jako nabídku Azure.
Hodnocení serveru Azure Migrate v současné době nepodporuje ceny založené na smlouva Enterprise (EA). Pokud chcete toto omezení obejít, použijte jako nabídku Azure průběžné **platby** a pomocí vlastnosti **slevy** určete vlastní slevu, kterou dostanete. [Další informace o možnostech přizpůsobení hodnocení](https://aka.ms/migrate/selfhelp/eapricing)

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready"></a>Proč hodnocení serveru označí moje virtuální počítače Linux jako podmíněně připravené?
V posuzování serveru je známá mezera, která brání tomu, aby zjistila podverzi operačního systému Linux nainstalovaného na místních virtuálních počítačích (například pro RHEL 6,10, aktuálně posouzení serveru detekuje pouze RHEL 6 jako verzi operačního systému). Vzhledem k tomu, že Azure schválí pouze konkrétní verze systému Linux, jsou virtuální počítače se systémem Linux v současnosti označeny jako podmíněně připravené v rámci posouzení serveru. Můžete zjistit, jestli operační systém Linux běžící na místním virtuálním počítači je v Azure schválený, v [dokumentaci podpory Azure Linux.](https://aka.ms/migrate/selfhost/azureendorseddistros). Po ověření schválené distribuce můžete toto upozornění ignorovat.

### <a name="why-does-the-vm-sku-recommended-by-server-assessment-have-more-cores-and-more-memory-than-whats-allocated-on-premises"></a>Proč je SKU virtuálního počítače doporučené pro posouzení serveru více jader a více paměti, než kolik je přiděleno místně?
Doporučení skladové položky virtuálního počítače v nástroji Hodnocení serverů závisí na vlastnostech posouzení. V posouzení serveru můžete vytvořit dva druhy posouzení: *Založené na výkonu* a *jako místní*. U posouzení na základě výkonu považuje server hodnocení za využití dat z místních virtuálních počítačů (CPU, paměti, disku a sítě) k určení správné cílové SKU virtuálního počítače pro vaše místní virtuální počítače. Pro určení velikosti na základě výkonu je navíc faktor komfortu zvážen při určování efektivního využití. Pro místní velikost se data o výkonu neberou v úvahu a na základě toho, co se přiděluje místně, se doporučuje cílová skladová jednotka.

Řekněme například, že se jedná o místní virtuální počítač se 4 jádry a 8 GB paměti s 50% využitím procesoru a 50% využití paměti a že se v posouzení určí faktor komfortu 1,3. Pokud jsou kritéria změny velikosti posouzení **místní**, doporučuje se skladová položka virtuálního počítače Azure se 4 jádry a 8 gigabajty (GB) paměti.

Předpokládejme však, že kritéria změny velikosti jsou založená na výkonu. Na základě efektivního využití procesoru a paměti (50% ze 4 jader × 1,3 = 2,6 jader a 50% z 8 GB paměti * 1,3 = 5,3-GB paměti) by se měla použít SKU nejlevnější virtuálního počítače se čtyřmi jádry (s nejbližším podporovaným počtem jader) a 8 GB paměti (nejbližší podporovaná velikost paměti). [Další informace o určování velikosti v nástroji Hodnocení serverů](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)

### <a name="why-is-the-disk-sku-recommended-by-server-assessment-bigger-than-whats-allocated-on-premises"></a>Proč je disková jednotka doporučená pro posouzení serveru větší, než je přidělená místně?
Velikost disku v posuzování serveru závisí na dvou vlastnostech posouzení: kritéria změny velikosti a typ úložiště. Pokud jsou kritéria změny velikosti **založená na výkonu** a typ úložiště je nastaven na hodnotu **automaticky**, považují se hodnoty IOPS a propustnosti disku za účelem identifikace typu cílového disku (HDD úrovně Standard, SSD úrovně Standard nebo prémiových disků). Pak se doporučuje disková jednotka disku z typu disku a toto doporučení posuzuje požadavky na velikost místního disku. Pokud jsou kritéria změny velikosti v **závislosti na výkonu**a typ úložiště je **Premium**, doporučuje se SKU na disku Premium v Azure na základě požadavků na vstupně-výstupní operace, propustnost a velikost na místním disku. Stejná logika se používá k provedení velikosti disku, pokud jsou kritéria změny velikosti **místní** a typ úložiště je **HDD úrovně Standard**, **SSD úrovně Standard**nebo **Premium**.

Například pokud máte místní disk s 32 GB paměti, ale agregované vstupně-výstupní operace čtení a zápisu pro disk je 800 IOPS, vyhodnocování serveru bude doporučit typ disku Premium (kvůli vyššímu počtu požadavků IOPS) a pak doporučit SKU disku, které může podporovat. požadovaný počet vstupně-výstupních operací a velikosti. Nejbližší shodou by v tomto případě byla skladová položka P15 (256 GB, 1 100 IOPS). I když velikost požadovaná místním diskem byla 32 GB, vyhodnocování serveru doporučuje větší disk kvůli vysokému počtu požadavků na požadavky na místní disk.

### <a name="why-does-my-assessment-report-indicate-percentageofcoresutilizedmissing-or-percentageofmemoryutilizedmissing-for-some-vms"></a>Proč má moje zpráva o posouzení označení "PercentageOfCoresUtilizedMissing" nebo "PercentageOfMemoryUtilizedMissing" pro některé virtuální počítače?
Tyto problémy jsou hlášeny, když zařízení Azure Migrate nedokáže shromáždit údaje o výkonu pro místní virtuální počítače. K tomuto chování může dojít, pokud jsou virtuální počítače vypnuté po dobu trvání při vytváření posouzení (poslední den/jeden týden/jeden měsíc). Zařízení nemůže shromažďovat údaje o výkonu pro virtuální počítač, když je vypnutý. Pokud nechybí pouze čítače paměti a snažíte se vyhodnotit virtuální počítače Hyper-V, ověřte, zda je na těchto virtuálních počítačích povolena dynamická paměť. U virtuálních počítačů, které nemají povolenou dynamickou paměť, nemůže Azure Migrate zařízení shromažďovat data o využití paměti. Tento problém se týká jenom virtuálních počítačů Hyper-V, ne virtuálních počítačů VMware. Pokud některý z čítačů výkonu chybí, Azure Migrate posouzení serveru se vrátí k přiděleným jádrům a paměti a doporučí odpovídající velikost virtuálního počítače.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>Jsou náklady na licenci na virtuální počítač zahrnuté do výpočetních nákladů na operační systém vyhodnocené vyhodnocením serveru?
Posouzení serveru aktuálně zohledňuje náklady na licenci na operační systém jenom pro počítače s Windows. Náklady na licenci na operační systém pro počítače se systémem Linux se momentálně nepovažují.

### <a name="what-impact-do-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Jaký dopad má využití historie výkonu a percentilu na velikost doporučení?
Tyto vlastnosti se vztahují pouze na změny velikosti na základě výkonu. Nástroj Hodnocení serverů průběžně shromažďuje data o výkonu místních počítačů a používá je k doporučení skladové položky virtuálního počítače a disku v Azure. Tato data o výkonu shromažďuje vyhodnocení serveru následujícím způsobem:
- Zařízení Azure Migrate průběžně profiluje místní prostředí za účelem shromažďování dat o využití v reálném čase každých 20 sekund pro virtuální počítače VMware a každých 30 sekund pro virtuální počítače Hyper-V.
- Zařízení shrnuje 20 sekund a 30 sekund vzorků, aby bylo možné vytvořit jeden datový bod každých 10 minut. Pokud chcete vytvořit jeden datový bod, zařízení vybere nejvyšší hodnotu ze všech ukázek 20 sekund a 30 sekund a pak je pošle do Azure.
- Když v nástroji Hodnocení serverů vytvoříte posouzení, na základě doby výkonu a hodnoty percentilu historie výkonu se zjistí reprezentativní hodnota efektivního využití. Například pokud je historie výkonu jeden týden a procento využití je 95., Azure Migrate seřadí všechny 10 minutové ukázkové body za poslední týden ve vzestupném pořadí a pak jako reprezentativní hodnotu vybere 95. percentil.
Hodnota 95. percentilu zajišťuje, že budete ignorovat jakékoli odlehlé hodnoty, které mohou být zahrnuty, pokud vyberete 99 percentil. Pokud chcete vybrat špičku pro období a nechcete vyznačit žádné z nich, měli byste vybrat 99 percentil pro využití percentilu.

## <a name="dependency-visualization-issues"></a>Problémy s vizualizací závislostí

### <a name="i-cant-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Nedaří se mi najít funkci Vizualizace závislostí pro Azure Government projekty.

Azure Migrate závisí na Service Map funkce Vizualizace závislostí. Protože Service Map v tuto chvíli není v Azure Government k dispozici, tato funkce není v Azure Government dostupná.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>Nainstaloval (a) jsem Microsoft Monitoring Agent (MMA) a Agent závislostí na místních virtuálních počítačích, ale závislosti se teď zobrazují na portálu Azure Migrate.

Po nainstalování agentů Azure Migrate k zobrazení závislostí na portálu obvykle trvá 15-30 minut. Pokud jste čekali na více než 30 minut, ujistěte se, že MMA může komunikovat s pracovním prostorem OMS pomocí následujících kroků.

Pro virtuální počítače s Windows:
1. Otevřete ovládací panely a spusťte Microsoft Monitoring Agent.
2. Na kartě **Azure Log Analytics (OMS)** v dialogovém okně **vlastnosti Microsoft Monitoring Agent** se ujistěte, že je **stav** pracovního prostoru zelený.
3. Pokud stav není zelený, zkuste odebrat pracovní prostor a znovu ho přidat do MMA.

      ![Dialogové okno Vlastnosti MMA](./media/troubleshooting-general/mma-status.png)

V případě virtuálního počítače se systémem Linux ověřte, zda byly příkazy instalace pro MMA a agenta závislostí úspěšné.

### <a name="what-operating-systems-does-mma-support"></a>Jaké operační systémy MMA podporuje?

 [Tady je seznam operačních systémů Windows, které podporuje MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
A [tady je seznam operačních systémů Linux, které podporuje MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-operating-systems-does-the-dependency-agent-support"></a>Které operační systémy podporují agenta závislostí?

[Tady je seznam [operačních systémů Windows a Linux, které Azure monitor pro virtuální počítače podporuje](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems).

### <a name="i-cant-visualize-dependencies-in-azure-migrate-for-more-than-a-one-hour-duration"></a>Nejde vizualizovat závislosti v Azure Migrate po dobu delší než 1 hodinu.
V Azure Migrate můžete vizualizovat závislosti až po dobu jedné hodiny. I když Azure Migrate umožňuje přejít zpět k určitému datu za poslední měsíc, maximální doba, po kterou můžete vizualizovat závislosti, je jedna hodina.

Například můžete použít funkci časového trvání na mapě závislostí k zobrazení závislostí včera, ale můžete je zobrazit jenom za dobu jedné hodiny. Můžete ale použít protokoly Azure Monitor k dotazování [dat závislosti](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) v delší době trvání.

### <a name="i-cant-visualize-dependencies-for-groups-that-have-more-than-10-vms"></a>Nejde vizualizovat závislosti pro skupiny, které mají víc než 10 virtuálních počítačů.
Můžete [vizualizovat závislosti pro skupiny](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) , které mají až 10 virtuálních počítačů. Pokud máte skupinu s víc než 10 virtuálními počítači, doporučujeme rozdělit skupinu do menších skupin a vizualizovat závislosti.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Nainstaloval jsem agenty a pomocí Vizualizace závislostí vytvořil skupiny. Nyní po převzetí služeb při selhání se v počítačích zobrazí akce instalovat agenta namísto příkazu "Zobrazit závislosti".
* Po plánovaném nebo neplánovaném převzetí služeb při selhání jsou místní počítače vypnuté a v Azure se naplní ekvivalentní počítače. Tyto počítače získají jinou adresu MAC. Můžou získat jinou IP adresu na základě toho, jestli se uživatel rozhodl zachovat místní IP adresu.

  Pokud se liší adresy MAC i IP, Azure Migrate nepřidruží místní počítače k žádným Service Mapm datům závislostí. Místo toho vyzve uživatele k instalaci agentů místo zobrazení závislostí.
* Po testovacím převzetí služeb při selhání budou místní počítače zapnuté podle očekávání. Ekvivalentní počítače propracované v Azure získají jinou adresu MAC a můžou získat různé IP adresy. Pokud uživatel neblokuje odchozí přenos Azure Monitor protokolu z těchto počítačů, Azure Migrate nepřidruží místní počítače k žádným datům závislosti Service Map a požádá uživatele o instalaci agentů namísto zobrazení závislostí.

## <a name="collect-azure-portal-logs"></a>Shromažďovat protokoly Azure Portal

**Návody shromažďovat protokoly Azure Portal síťových přenosů?**

1. Otevřete prohlížeč, pokračujte [na portál](https://portal.azure.com)a přihlaste se.
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
