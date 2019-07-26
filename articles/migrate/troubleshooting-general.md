---
title: Řešení potíží s Azure Migrate | Microsoft Docs
description: Poskytuje přehled známých problémů služby Azure Migrate a Rady pro odstraňování běžných chyb.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: raynew
ms.openlocfilehash: 15d3809b9a028fd2495c504e9bf19251dd051520
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372578"
---
# <a name="troubleshoot-azure-migrate"></a>Řešení problémů s Azure Migrate

[Azure Migrate](migrate-services-overview.md) poskytuje centrum nástrojů Microsoftu pro účely posouzení a migrace a také nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců. Tento dokument poskytuje pomoc při řešení chyb pomocí Azure Migrate Azure Migrate: Vyhodnocování a Azure Migrate serveru: Migrace serverů.

## <a name="azure-migrate-project-issues"></a>Problémy s Azure Migrate projektu

### <a name="i-am-unable-to-find-my-existing-azure-migrate-project"></a>Nedaří se mi najít existující Azure Migrate projekt.

Existují [dvě verze](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) Azure Migrate. V závislosti na verzi, ve které jste projekt vytvořili, postupujte podle následujících kroků a vyhledejte projekt:

1. Pokud hledáte projekt vytvořený s zkontrolují verzí (starým prostředím) Azure Migrate, vyhledejte projekt pomocí následujících kroků.

    1. Přejít na [Azure Portal](https://portal.azure.com), vyhledejte **Azure Migrate**.
    2. Na řídicím panelu Azure Migrate se zobrazí banner s přístupem k starším projektům. Tento banner se zobrazí pouze v případě, že jste vytvořili projekt se starým prostředím. Klikněte na banner.

    ![Přístup ke stávajícím projektům](./media/troubleshooting-general/access-existing-projects.png)

    3. Nyní se zobrazí seznam existujících projektů vytvořených v předchozí verzi Azure Migrate.

2. Pokud hledáte projekt, který byl vytvořen s aktuální verzí (nové prostředí), vyhledejte projekt pomocí následujících kroků.

    1. Přejít na [Azure Portal](https://portal.azure.com), vyhledejte **Azure Migrate**.
    2. Na řídicím panelu Azure Migrate přejděte na stránku **servery** v levém podokně a v pravém horním rohu vyberte **změnit** :

    ![Přepnout na existující Azure Migrate projekt](./media/troubleshooting-general/switch-project.png)

    3. Vyberte příslušné **předplatné** a **proveďte migraci projektu**.

### <a name="i-am-unable-to-create-a-second-azure-migrate-project"></a>Nedaří se mi vytvořit druhý Azure Migrate projekt.

Chcete-li vytvořit nový projekt Azure Migrate, postupujte podle následujících kroků.

1. Přejít na [Azure Portal](https://portal.azure.com), vyhledejte **Azure Migrate**.
2. Na řídicím panelu Azure Migrate přejděte na stránku **servery** v levém podokně a v pravém horním rohu vyberte **změnit** :

   ![Změnit Azure Migrate projekt](./media/troubleshooting-general/switch-project.png)

3. Chcete-li vytvořit nový projekt, vyberte **možnost klikněte sem** , jak je znázorněno na následujícím snímku obrazovky:

   ![Vytvořit druhý Azure Migrate projekt](./media/troubleshooting-general/create-new-project.png)

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Které geografické oblasti Azure podporuje Azure Migrate?

Seznam pro [VMware sem](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) můžete najít tady a pro [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>Odstranění Azure Migrate projektů a přidruženého pracovního prostoru Log Analytics

Když odstraníte Azure Migrate projekt, odstraní se i projekt migrace spolu s metadaty o zjištěných počítačích. Pokud jste ale k nástroji pro vyhodnocení serveru připojili Log Analytics pracovní prostor, automaticky se pracovní prostor Log Analytics neodstraní. Je to proto, že je možné použít stejný pracovní prostor Log Analytics pro více případů použití. Pokud chcete odstranit i pracovní prostor Log Analytics, musíte to provést ručně.

1. Přejděte do pracovního prostoru Log Analytics připojeného k projektu.
     1. Pokud jste ještě neodstranili projekt migrace, můžete najít odkaz na pracovní prostor na stránce Přehled posouzení serveru v části základy.

     ![Pracovní prostor LA](./media/troubleshooting-general/loganalytics-workspace.png)

     2. Pokud jste už projekt migrace odstranili, v levém podokně Azure Portal vyberte **skupiny prostředků** . Přejděte do skupiny prostředků, ve které se pracovní prostor vytvořil, a pak ho vyhledejte.
2. Pokud chcete pracovní prostor odstranit, postupujte podle pokynů [v tomto článku](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) .

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Projekt migrace se nepovedlo vytvořit. požadavky na chyby *musí obsahovat hlavičky identity uživatele* .

K tomuto problému může dojít pro uživatele, kteří nemají přístup k tenantovi Azure Active Directory (Azure AD) organizace. Při prvním přidání uživatele do tenanta služby Azure AD obdrží pozvánku e-mailu, aby se připojil k tenantovi. Uživatelé musí přejít k e-mailu a přijmout pozvánku k úspěšnému přidání do tenanta. Pokud nemůžete tento e-mail zobrazit, obraťte se na uživatele, který už má přístup ke klientovi, a požádejte ho, aby pozvánku poslal znovu, a to pomocí kroků uvedených [tady](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Po přijetí e-mailu s pozvánkou musíte otevřít e-mail a kliknout na odkaz v e-mailu, abyste pozvánku přijali. Až to uděláte, musíte se odhlásit od Azure Portal a znovu se přihlásit, ale aktualizace prohlížeče nebude fungovat. Pak můžete zkusit projekt migrace vytvořit.

## <a name="appliance-issues"></a>Problémy se zařízením

### <a name="deployment-of-azure-migrate-appliance-for-vmware-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>Nasazení zařízení Azure Migrate pro VMware se nepovedlo kvůli této chybě: Zadaný soubor manifestu je neplatný: Neplatný záznam manifestu OVF

1. Zkontrolujte jeho hodnotu hash a ověřte, zda je soubor s Azure Migratemi VAJÍČKy zařízení správně stažen. Informace o tom, jak zkontrolovat hodnotu hash, najdete v tomto [článku](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Pokud hodnota hash není shodná, Stáhněte si znovu soubor vajíček a zkuste nasazení zopakovat.
2. Pokud se zase nepodaří a pokud nasazujete soubor OVF pomocí klienta VMware vSphere, zkuste ho nasadit pomocí webového klienta vSphere. Pokud se chyba stále nedaří, zkuste použít jiný webový prohlížeč.
3. Pokud používáte webového klienta vSphere a pokusíte se ho nasadit na vCenter Server 6,5 nebo 6,7, zkuste nasadit VAJÍČKu přímo na hostiteli ESXi pomocí následujících kroků:
   - Připojte se k hostiteli ESXi přímo (místo vCenter Server) pomocí webového klienta (https://<*IP adresa hostitele*>/UI).
   - Přejít na inventáře **Domů** > 
   - Klikněte na **soubor** > **nasadit šablonu OVF**. Přejděte k VAJÍČKám a dokončete nasazení.
4. Pokud se nasazení stále nedaří, obraťte se na podporu Azure Migrate.

### <a name="appliance-is-not-able-to-connect-to-the-internet"></a>Zařízení se nemůže připojit k Internetu.

K tomu může dojít, když je počítač, který používáte, za proxy serverem. Ujistěte se, že přihlašovací údaje pro autorizaci zadáte, pokud je server proxy vyžaduje.
Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, nezapomeňte přidat tyto požadované adresy URL do seznamu povolených adres:

Scénář | Seznam adres URL
--- | ---
Posouzení serveru pro VMware | [Tady](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
Posouzení serveru pro Hyper-V | [Tady](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
Migrace serveru pro VMware (bez agentů) | [Tady](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
Migrace serveru pro VMware (založená na agentovi) | [Tady](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
Migrace serveru pro Hyper-V | [Tady](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

Pokud k připojení k Internetu používáte zachycení proxy serveru, budete muset importovat certifikát proxy serveru do virtuálního počítače zařízení. Certifikát proxy serveru můžete importovat pomocí postupu popsaného [tady](https://docs.microsoft.com/azure/migrate/concepts-collector).

### <a name="error-802-date-and-time-synchronization-error"></a>Chyba 802: Chyba synchronizace data a času

Hodiny serveru můžou být nesynchronizovány s aktuálním časem více než pěti minutami. Změňte čas času na virtuálním počítači kolektoru tak, aby odpovídal aktuálnímu času, a to následujícím způsobem:

1. Otevřete příkazový řádek správce na virtuálním počítači.
2. Chcete-li kontrolovat časové pásmo, spusťte příkaz W32tm/TZ.
3. Chcete-li synchronizovat čas, spusťte příkaz W32tm/Resync.


### <a name="error-unabletoconnecttoserver"></a>Chyba UnableToConnectToServer

Nejde se připojit k vCenter Serveru Servername.com:9443, protože došlo k chybě: Na https://Servername.com:9443/sdk nenaslouchal žádný koncový bod, který by mohl tuto zprávu přijmout.

Ověřte, jestli používáte nejnovější verzi zařízení kolektoru. Pokud ne, upgradujte zařízení na [nejnovější verzi](https://docs.microsoft.com/azure/migrate/concepts-collector).

Pokud k problému dochází v nejnovější verzi, může to být způsobeno tím, že počítač kolektoru nemůže přeložit zadaný název vCenter Server nebo je zadaný port nesprávný. Ve výchozím nastavení se kolektor pokusí připojit k portu číslo 443, pokud není zadaný port.

1. Zkuste provést test Servername.com z sběrného počítače pomocí testu.
2. Pokud krok 1 selže, zkuste se k serveru vCenter připojit přes IP adresu.
3. Zjistěte správné číslo portu pro připojení k serveru vCenter.
4. Nakonec zkontrolujte, jestli server vCenter je spuštěný.


### <a name="the-appliance-could-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60052"></a>Zařízení se nepovedlo úspěšně zaregistrovat do projektu Azure Migrate (ID chyby: 60052)

Tato chyba je způsobená nedostatečnými oprávněními k účtu Azure, který se použil k registraci zařízení. Ujistěte se, že uživatelský účet Azure použitý k registraci zařízení má aspoň oprávnění Přispěvatel v rámci předplatného. [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) o požadovaných rolích a oprávněních Azure.

### <a name="the-appliance-could-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60039"></a>Zařízení se nepovedlo úspěšně zaregistrovat do projektu Azure Migrate (ID chyby: 60039)

Azure Migrate projekt, který jste vybrali k registraci zařízení, se nenajde, což způsobí selhání registrace. Otevřete Azure Portal a ověřte, jestli projekt ve vaší skupině prostředků existuje. Pokud projekt neexistuje, vytvořte ve skupině prostředků nový Azure Migrate projekt a znovu zaregistrujte zařízení. [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) o vytvoření nového projektu Azure Migrate.

### <a name="azure-key-vault-management-operation-failed-error-id-60030-60031"></a>Operace správy trezoru klíčů Azure se nezdařila (ID chyby: 60030, 60031)

Ujistěte se, že uživatelský účet Azure použitý k registraci zařízení má aspoň oprávnění Přispěvatel v rámci předplatného. Také ověřte, zda má účet přístup k Key Vault zadané v chybové zprávě a opakujte operaci. Pokud se problém nevyřeší, obraťte se na podporu Microsoftu. [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) o požadovaných rolích a oprávněních Azure.

### <a name="discovery-could-not-be-initiated-due-to-the-error-the-operation-failed-for-the-given-list-of-hosts-or-clusters-error-id-60028"></a>Zjišťování nebylo možné zahájit z důvodu chyby. Operace se pro daný seznam hostitelů nebo clusterů nezdařila (ID chyby: 60028)

Na hostitelích uvedených v chybě se nepodařilo spustit zjišťování, protože došlo k potížím při přístupu nebo načítání informací o virtuálním počítači. ostatní hostitelé, které jste přidali, se úspěšně přidaly. Přidejte znovu hostitele v této chybě pomocí možnosti **Přidat hostitele** . Pokud dojde k chybě ověřování, přečtěte si pokyny k nápravě a opravte chyby a zkuste znovu **Uložit a spustit zjišťování** .

### <a name="azure-active-directory-aad-operation-failed-the-error-occurred-while-creating-or-updating-the-aad-application-error-id-60025"></a>Operace Azure Active Directory (AAD) se nezdařila. Při vytváření nebo aktualizaci aplikace AAD došlo k chybě (ID chyby: 60025)

Uživatelský účet Azure, který se používá k registraci zařízení, nemá přístup k aplikaci AAD uvedené v chybové zprávě. Ověřte, zda jste vlastníkem aplikace AAD. [Přečtěte si další informace](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) o oprávněních aplikace AAD.


## <a name="discovery-issues"></a>Problémy se zjišťováním

### <a name="i-started-discovery-but-i-dont-see-the-discovered-vms-on-azure-portal-server-assessment-and-server-migration-tiles-show-a-status-of-discovery-in-progress"></a>Zahájil (a) jsem zjišťování, ale zjištěné virtuální počítače se na Azure Portal nezobrazily. Dlaždice Server Assessment a migrace serveru zobrazují stav "Probíhá zjišťování".
Po spuštění zjišťování ze zařízení počkejte určitou dobu, než se zjištěné počítače zobrazí na Azure Portal. Zjišťování VMware trvá přibližně 15 minut a přibližně 2 minuty na přidané hostitele pro zjišťování technologie Hyper-V. Pokud se vám nadále zobrazuje "zjišťování probíhá", a to i po tomto čase, klikněte na tlačítko **aktualizovat** na kartě **servery** . Mělo by se zobrazit počet zjištěných serverů na dlaždicích Server pro vyhodnocení a migraci serveru.


### <a name="i-am-using-the-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>Používám zařízení, které nepřetržitě zjišťuje místní prostředí, ale virtuální počítače, které jsou v místním prostředí odstraněny, se pořád zobrazují na portálu.

Data zjišťování shromážděná zařízením zabere až 30 minut, než se na portálu projeví. Pokud nevidíte informace o aktuálním stavu ani po 30 minutách, vydejte aktualizaci dat pomocí následujících kroků:

1. Na serverech > Azure Migrate: Vyhodnocování serveru klikněte na **Přehled**.
2. V části **Spravovat**klikněte na **Agent Health**
3. Kliknutím na možnost **agenta aktualizujete**. V seznamu agentů se zobrazí následující možnost.
4. Počkejte na dokončení operace aktualizace. Ověřte, že se vám na vašich virtuálních počítačích můžete podívat na aktuální informace.

### <a name="i-dont-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>Nedaří se mi získat nejnovější informace o místních virtuálních počítačích na portálu.

Data zjišťování shromážděná zařízením zabere až 30 minut, než se na portálu projeví. Pokud nevidíte informace o aktuálním stavu ani po 30 minutách, vydejte aktualizaci dat pomocí následujících kroků:

1. Na serverech > Azure Migrate: Vyhodnocování serveru klikněte na **Přehled**.
2. V části **Spravovat**klikněte na **Agent Health**
3. Kliknutím na možnost **agenta aktualizujete**. Tato možnost se zobrazí pod seznamem agentů.
4. Počkejte na dokončení operace aktualizace. Teď byste měli vidět aktuální informace na vašich virtuálních počítačích.

### <a name="unable-to-connect-to-hosts-or-cluster-as-the-server-name-cannot-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>Nepovedlo se připojit k hostitelům nebo clusteru, protože název serveru nejde přeložit. Kód chyby WinRM: 0x803381B9 (ID chyby: 50004)
K této chybě dojde, pokud služba DNS obsluhující zařízení nemůže vyřešit zadaný název clusteru nebo hostitele. Pokud se tato chyba zobrazuje v clusteru, zkuste zadat plně kvalifikovaný název domény clusteru.

Tato chyba se může zobrazit i pro hostitele v clusteru. V takovém případě se zařízení může připojit ke clusteru. Ale cluster vrátil názvy hostitelů, které nejsou plně kvalifikované názvy domén.

Chcete-li tuto chybu vyřešit, aktualizujte soubor hostitelů na zařízení přidání mapování IP adresy a názvů hostitelů.
1. Otevřete Poznámkový blok jako uživatel s oprávněními správce. Otevřete soubor C:\Windows\System32\Drivers\etc\hosts.
2. Přidejte IP adresu a název hostitele do řádku. Opakujte pro každého hostitele nebo cluster, kde se zobrazí tato chyba.
3. Uložte a zavřete soubor Hosts.
4. Můžete zjistit, jestli se zařízení může připojit k hostitelům pomocí aplikace pro správu zařízení. Po 30 minutách byste si měli být schopni zobrazit nejnovější informace o těchto hostitelích na Azure Portal.


## <a name="assessment-issues"></a>Problémy posouzení

### <a name="azure-readiness-issues"></a>Problémy s připraveností na Azure

Problém | Náprava
--- | ---
Nepodporovaný typ spuštění | Azure nepodporuje virtuální počítače s typem spouštění EFI. Před spuštěním migrace doporučujeme převést typ spouštění na systém BIOS. <br/><br/>K migraci takových virtuálních počítačů můžete použít migraci Azure Migrate serveru, protože při migraci převede typ spouštění virtuálního počítače na systém BIOS.
Podmíněně podporované operační systémy Windows | Operační systém předal svůj konec platnosti a pro [podporu v Azure](https://aka.ms/WSosstatement)potřebuje vlastní smlouvu o podpoře (CSA). před migrací do Azure zvažte možnost upgradovat operační systém.
Nepodporovaný operační systém Windows | Azure podporuje jenom [vybrané verze operačních systémů Windows](https://aka.ms/WSosstatement). před migrací do Azure zvažte možnost upgradovat operační systém počítače.
Podmíněně schválený operační systém Linux | Azure schválí jenom [vybrané verze operačního systému Linux](../virtual-machines/linux/endorsed-distros.md). před migrací do Azure zvažte možnost upgradovat operační systém počítače.
Neschválený operační systém Linux | Počítač se může spustit v Azure, ale Azure neposkytuje žádnou podporu operačního systému. před migrací do Azure zvažte možnost upgradovat operační systém na [schválenou verzi systému Linux](../virtual-machines/linux/endorsed-distros.md) .
Neznámý operační systém | Operační systém virtuálního počítače se ve vCenter Server zadal jako jiný, protože Azure Migrate nedokáže identifikovat připravenost virtuálního počítače Azure. Než počítač migrujete, ujistěte se, že Azure [podporuje](https://aka.ms/azureoslist) operační systém běžící v počítači.
Nepodporovaná bitová verze operačního systému | Virtuální počítače s 32 operačním systémem se můžou v Azure spustit, ale před migrací do Azure se doporučuje upgradovat operační systém virtuálního počítače z 32-bitů na 64.
Vyžaduje předplatné sady Visual Studio. | Počítač má spuštěný klientský operační systém Windows, který je podporován pouze v rámci předplatného sady Visual Studio.
Pro požadovaný výkon úložiště se nenašel žádný virtuální počítač. | Výkon úložiště (IOPS/propustnost) vyžadovaný pro tento počítač překračuje podporu virtuálních počítačů Azure. Před migrací snižte požadavky na úložiště pro daný počítač.
Pro požadovaný výkon sítě nebyl nalezen virtuální počítač. | Výkon sítě (v/v) vyžadovaný pro tento počítač překračuje podporu virtuálních počítačů Azure. Snižte požadavky na síť pro daný počítač.
Virtuální počítač se nenašel v zadaném umístění. | Před migrací použijte jiné cílové umístění.
Jeden nebo více nevhodných disků. | Jeden nebo více disků připojených k virtuálnímu počítači nesplňuje požadavky Azure. U každého disku připojeného k virtuálnímu počítači zajistěte, aby velikost disku byla < 4 TB. Pokud ne, zmenšete velikost disku před migrací do Azure. Ujistěte se, že [disky virtuálních počítačů spravované](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)službou Azure podporují výkon (IOPS/propustnost), který vyžaduje každý disk.   
Jeden nebo více nevhodných síťových adaptérů. | Před migrací odeberte nepoužívané síťové adaptéry z počítače.
Počet disků překračuje limit. | Před migrací z počítače odeberte nepoužívané disky.
Velikost disku překračuje limit. | Azure podporuje disky o velikosti až 4 TB. Před migrací zmenšete disky na méně než 4 TB.
V zadaném umístění není k dispozici žádný disk. | Před migrací zajistěte, aby byl disk v cílovém umístění.
Pro zadanou redundanci není k dispozici žádný disk. | Disk by měl používat typ úložiště redundance definovaný v nastavení hodnocení (ve výchozím nastavení LRS).
Z důvodu interní chyby není možné určit vhodnost disku. | Zkuste vytvořit nové vyhodnocení pro skupinu.
Virtuální počítač s požadovaný počtem jader a požadovanou pamětí se nenašel. | Azure nemůže navýšit vhodný typ virtuálního počítače. Před migrací Snižte velikost paměti a počtu jader místního počítače.
Kvůli vnitřní chybě se nepovedlo určit vhodnost virtuálního počítače. | Zkuste vytvořit nové vyhodnocení pro skupinu.
Kvůli vnitřní chybě se nepovedlo určit vhodnost pro jeden nebo více disků. | Zkuste vytvořit nové vyhodnocení pro skupinu.
Kvůli vnitřní chybě se nepovedlo určit vhodnost pro jeden nebo víc síťových adaptérů. | Zkuste vytvořit nové vyhodnocení pro skupinu.

### <a name="i-am-unable-to-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>Ve vlastnostech posouzení nejde zadat smlouva Enterprise (EA) jako nabídku Azure?
Azure Migrate: Hodnocení serverů v současné době nepodporuje ceny založené na smlouvě Enterprise. Alternativním řešením je jako nabídku Azure použít průběžné platby a pomocí vlastnosti Discount určit případné individuální slevy. [Další informace o možnostech přizpůsobení hodnocení](https://aka.ms/migrate/selfhelp/eapricing)

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready-is-there-anything-i-need-to-do-to-fix-this"></a>Proč vyhodnocování serveru označí moje virtuální počítače Linux jako podmíněně připravené. Je něco, co je potřeba vyřešit?
V nástroji Hodnocení serverů existuje známý nedostatek, kdy nástroj nedokáže rozpoznat podverzi operačního systému Linux nainstalovaného na místních virtuálních počítačích (například u RHEL 6.10 v současné době nástroj Hodnocení serverů jako verzi operačního systému rozpozná pouze RHEL 6). Vzhledem k tomu, že Azure schvaluje pouze určité verze Linuxu, virtuální počítače s Linuxem se v současné době v nástroji Hodnocení serverů označují jako připravené s podmínkou. Můžete ručně zkontrolovat, jestli je operační systém Linux běžící na místním virtuálním počítači schválen v Azure, v [dokumentaci podpory Azure Linux.](https://aka.ms/migrate/selfhost/azureendorseddistros). Jakmile ověříte, že je distribuce schválená, můžete toto upozornění ignorovat.

### <a name="the-vm-sku-recommended-by-server-assessment-has-more-number-of-cores-and-a-larger-memory-size-than-what-is-allocated-on-premises-why-is-that-so"></a>SKU virtuálního počítače doporučeného posouzením serveru má větší počet jader a větší velikost paměti než místní přidělení. Proč to znamená?
Doporučení skladové položky virtuálního počítače v nástroji Hodnocení serverů závisí na vlastnostech posouzení. V nástroji Hodnocení serverů můžete vytvořit dva druhy posouzení: posouzení na základě výkonu a posouzení na základě místního nastavení. U posouzení na základě výkonu považuje server hodnocení za využití dat z místních virtuálních počítačů (CPU, paměti, disku a sítě) k určení správné cílové SKU virtuálního počítače pro vaše místní virtuální počítače. Při určování velikosti na základě výkonu se navíc při identifikaci efektivního využití bere v úvahu i faktor komfortu. V případě určování velikosti na základě místního nastavení se data o využití nezohledňují a cílová skladová položka se doporučí na základě toho, co je přiděleno v místním prostředí.

Řekněme například, že se jedná o místní virtuální počítač se 4 jádry a 8 GB paměti s 50% využitím procesoru a 50% využití paměti a že se v posouzení určí faktor komfortu 1,3. Pokud je kritérium změny velikosti posouzení stejné jako místní, doporučuje se SKU virtuálního počítače Azure se 4 jádry a 8 GB paměti. Pokud je ale kritérium změny velikosti založené na výkonu, vychází z efektivního využití procesoru a paměti (50% ze 4 jader × 1,3 = 2,6 jader a 50% 8 GB paměť * 1,3 = 5,3-GB paměti), doporučuje se SKU virtuálního počítače nejlevnější se čtyřmi jádry (nejbližší podporovaný počet jader) a 8 GB velikosti paměti (nejbližší podporovaná velikost paměti). [Další informace o určování velikosti v nástroji Hodnocení serverů](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)

### <a name="the-disk-sku-recommended-by-server-assessment-has-a-bigger-size-than-what-is-allocated-on-premises-why-is-that-so"></a>SKU disku, které doporučuje posouzení serveru, má větší velikost, než je přiděleno místně. Proč to znamená?
Určování velikosti disku v nástroji Hodnocení serverů závisí na dvou vlastnostech posouzení: kritérium určení velikosti a typ úložiště. Pokud má kritérium změny velikosti hodnotu "na základě výkonu" a typ úložiště je nastaven na hodnotu automaticky, považují se hodnoty IOPS a propustnosti disku za účelem určení typu cílového disku (HDD úrovně Standard, SSD úrovně Standard a prémiových disků). V závislosti na požadavcích na velikost místního disku se pak doporučí skladová položka disku v rámci daného typu. Pokud má kritérium změny velikosti hodnotu "na základě výkonu" a typ úložiště je Premium, doporučuje se skladová jednotka Premium v Azure na základě požadavků na vstupně-výstupní operace, propustnost a velikost na disku. Stejná logika se používá při určování velikosti disku, když je kritériem určení velikosti místní nastavení velikosti a typ úložiště je HDD úrovně Standard, SSD úrovně Standard nebo Premium.

Například pokud máte místní disk s 32 GB paměti, ale agregované vstupně-výstupní operace čtení a zápisu pro disk je 800 IOPS, posouzení serveru doporučí typ disku Premium (z důvodu vyšších požadavků IOPS) a pak doporučí SKU disku, který může podporovat požadovaný počet vstupně-výstupních operací a velikosti. Nejbližší shodou by v tomto případě byla skladová položka P15 (256 GB, 1 100 IOPS). Takže i když místní disk vyžadoval velikost 32 GB, nástroj Hodnocení serverů kvůli vysokým požadavkům místního disku na IOPS doporučil větší disk.

### <a name="why-does-my-assessment-report-say-percentageofcoresutilizedmissing-or-percentageofmemoryutilizedmissing-for-some-vms"></a>Proč moje zpráva o posouzení říká "PercentageOfCoresUtilizedMissing" nebo "PercentageOfMemoryUtilizedMissing" pro některé virtuální počítače?
Výše uvedené problémy se zobrazí, když zařízení Azure Migrate nedokáže shromáždit údaje o výkonu pro místní virtuální počítače. K tomu může dojít, pokud jsou virtuální počítače vypnuté po dobu, po kterou vytváříte hodnocení (poslední den/jeden týden/jeden měsíc), protože zařízení nedokáže shromažďovat údaje o výkonu pro virtuální počítač, když je vypnutý. Pokud v případě chybějících čítačů paměti dojde k vyhodnocování virtuálních počítačů Hyper-V, ověřte, jestli máte na těchto virtuálních počítačích povolenou dynamickou paměť. V tuto chvíli dochází k známému problému, protože Azure Migrate zařízení nemůže shromažďovat využití paměti pro virtuální počítače, které nemají povolenou dynamickou paměť. Upozorňujeme, že tento problém je jenom pro virtuální počítače Hyper-V, a ne pro virtuální počítače VMware. Pokud některý z čítačů výkonu chybí, Azure Migrate: Posouzení serveru se vrátí k přiděleným jádrům a paměti a doporučuje odpovídající velikost virtuálního počítače.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>Jsou náklady na licenci na virtuální počítač zahrnuté do výpočetních nákladů na operační systém vyhodnocené vyhodnocením serveru?
Nástroj Hodnocení serverů v současné době zohledňuje licenční náklady na operační systém pouze u počítačů s Windows. U počítačů s Linuxem se aktuálně licenční náklady na operační systém nezohledňují.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Jaký dopad má využití historie výkonu a percentilu na velikost doporučení?
Tyto vlastnosti se zohledňují pouze při určování velikosti na základě výkonu. Nástroj Hodnocení serverů průběžně shromažďuje data o výkonu místních počítačů a používá je k doporučení skladové položky virtuálního počítače a disku v Azure. Níže najdete vysvětlení způsobu, jakým nástroj Hodnocení serverů shromažďuje data o výkonu:
- Zařízení Azure Migrate průběžně profiluje místní prostředí za účelem shromažďování dat o využití v reálném čase každých 20 sekund pro virtuální počítače VMware a každých 30 sekund pro virtuální počítače Hyper-V.
- Zařízení seskupuje 20sekundové a 30sekundové vzorky a pro každých 10 minut vytvoří jeden datový bod. Jeden datový bod se vytvoří tak, že zařízení ze všech 20sekundových a 30sekundových vzorků vybere maximální hodnotu a odešle ji do Azure.
- Když v nástroji Hodnocení serverů vytvoříte posouzení, na základě doby výkonu a hodnoty percentilu historie výkonu se zjistí reprezentativní hodnota efektivního využití. Například pokud je historie výkonu jeden týden a procento využití je 95., Azure Migrate bude řadit všechny 10 minutových vzorků za poslední týden ve vzestupném pořadí a pak jako hodnotu zástupce vybrat 95. percentil.
Hodnota 95. percentilu zajistí, že se budou ignorovat případné odlehlé hodnoty, které by se mohly zohlednit, kdybyste vybrali 99. percentil. Pokud chcete vybrat maximální využití v určitém období a nechcete přijít o žádné odlehlé hodnoty, měli byste jako percentilové využití vybrat 99. percentil.

## <a name="dependency-visualization-issues"></a>Problémy s vizualizací závislostí

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Nedaří se mi najít funkci Vizualizace závislostí pro Azure Government projekty.

Azure Migrate závisí na Service Map funkce Vizualizace závislostí a protože Service Map aktuálně není v Azure Government k dispozici, tato funkce není k dispozici v Azure Government.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>Nainstaloval (a) jsem Microsoft Monitoring Agent (MMA) a Agent závislostí na místních virtuálních počítačích, ale závislosti se teď zobrazují na portálu Azure Migrate.

Jakmile nainstalujete agenty, Azure Migrate k zobrazení závislostí na portálu obvykle trvá 15-30 minut. Pokud jste čekali na více než 30 minut, ujistěte se, že agent MMA může komunikovat s pracovním prostorem OMS pomocí následujících kroků:

Pro virtuální počítače s Windows:
1. Otevřete **Ovládací panely** a spusťte **Microsoft Monitoring Agent**.
2. V automaticky otevíraném okně vlastností MMA přejít na kartu **Azure Log Analytics (OMS)** .
3. Ujistěte se, že je **stav** pracovního prostoru zelený.
4. Pokud stav není zelený, zkuste odebrat pracovní prostor a znovu ho přidat do MMA.
        ![Stav MMA](./media/troubleshooting-general/mma-status.png)

V případě virtuálního počítače se systémem Linux zajistěte, aby byly příkazy instalace pro MMA a agenta závislostí úspěšné.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Jaké jsou operační systémy, které MMA podporuje?

Seznam operačních systémů Windows, které podporuje MMA, [najdete tady](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
[Tady](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)je seznam operačních systémů Linux podporovaných nástrojem MMA.

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Jaké jsou operační systémy podporované agentem závislostí?

[Tady](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)je seznam operačních systémů Windows podporovaných agentem závislostí.
[Tady](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)je seznam operačních systémů Linux podporovaných agentem závislostí.

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Nedaří se mi vizualizovat závislosti v Azure Migrate po dobu více než jedné hodiny?
Azure Migrate umožňuje vizualizovat závislosti po dobu až jedné hodiny. I když vám Azure Migrate umožňuje přejít zpět k určitému datu v historii po dobu až posledních měsíců, maximální doba trvání, po kterou můžete vizualizovat závislosti, je až 1 hodina. Například můžete použít funkci časového trvání na mapě závislostí k zobrazení závislostí včera, ale lze je zobrazit pouze v jednom časovém intervalu. Můžete ale použít protokoly Azure Monitor k dotazování [dat závislosti](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) v delší době trvání.

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Nedaří se mi vizualizovat závislosti pro skupiny s více než 10 virtuálními počítači?
Můžete [vizualizovat závislosti pro skupiny](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) , které mají až 10 virtuálních počítačů. Pokud máte skupinu s víc než 10 virtuálními počítači, doporučujeme rozdělit skupinu do menších skupin a vizualizovat závislosti.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Nainstaloval jsem agenty a pomocí Vizualizace závislostí vytvořil skupiny. Nyní po převzetí služeb při selhání se v počítačích místo zobrazení závislostí zobrazí akce instalovat agenta.
* Po plánovaném nebo neplánovaném převzetí služeb při selhání jsou místní počítače vypnuté a v Azure se prosazuje ekvivalentní počítače. Tyto počítače získají jinou adresu MAC. Můžou získat jinou IP adresu na základě toho, jestli se uživatel rozhodl zachovat místní IP adresu, nebo ne. Pokud se adresa MAC i IP adresy liší, Azure Migrate nepřidruží místní počítače k žádným Service Map dat závislosti a požádá uživatele o instalaci agentů místo zobrazení závislostí.
* Po testovacím převzetí služeb při selhání budou místní počítače zapnuté podle očekávání. Ekvivalentní počítače propracované v Azure získají jinou adresu MAC a můžou získat jinou IP adresu. Pokud uživatel neblokuje odchozí Azure Monitor protokoluje provoz z těchto počítačů, Azure Migrate nepřidruží místní počítače k žádným Service Map datům závislosti a požádá uživatele o instalaci agentů namísto zobrazení závislostí.

## <a name="collect-azure-portal-logs"></a>Shromažďovat protokoly Azure Portal

**Návody shromažďovat protokoly Azure Portal síťových přenosů?**

1. Otevřete prohlížeč a přejděte [na portál](https://portal.azure.com)a přihlaste se.
2. Stisknutím klávesy F12 spusťte Vývojářské nástroje. V případě potřeby zrušte zaškrtnutí políčka **vymazat položky při navigaci**.
3. Klikněte na kartu **síť** a spusťte zachytávání síťového provozu:
   - V části Chrome vyberte **zachovat protokol**. Záznam by se měl spustit automaticky. Červený kroužek indikuje, že probíhá zachycení provozu. Pokud se nezobrazí, začněte kliknutím na černý kroužek.
   - V Microsoft Edge/IE se má záznam spustit automaticky. Pokud tomu tak není, klikněte na zelené tlačítko Přehrát.
4. Zkuste reprodukování chyby.
5. Po zjištění chyby při nahrávání, zastavení záznamu a uložení kopie zaznamenané aktivity:
   - V Chrome klikněte pravým tlačítkem myši a klikněte na **Uložit jako Har s obsahem**. Tím se komprimují a vyexportují protokoly jako soubor. har.
   - V Microsoft Edge/IE klikněte na ikonu **exportovat zaznamenaný přenos** . Tím se zkomprimuje a vyexportuje protokol.
6. Přejděte na kartu **Konzola** a vyhledejte všechna upozornění a chyby. Uložení protokolu konzoly:
   - V prohlížeči Chrome klikněte pravým tlačítkem myši kdekoli v protokolu konzoly. Vyberte **Uložit jako**, exportovat a protokol zip.
   - V Microsoft Edge/IE klikněte pravým tlačítkem na chyby a vyberte **Kopírovat vše**.
7. Zavřete Vývojářské nástroje.
