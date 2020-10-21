---
title: Řešení potíží s replikací v migraci virtuálních počítačů VMware bez agenta
description: Získat pomoc s chybami cyklu replikace
author: anvar-ms
ms.manager: bsiva
ms.author: anvar
ms.topic: troubleshooting
ms.date: 08/17/2020
ms.openlocfilehash: 2b653a0abbe89686c764a6a0885720cc746975c8
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92314734"
---
# <a name="troubleshooting-replication-issues-in-agentless-vmware-vm-migration"></a>Řešení potíží s replikací v migraci virtuálních počítačů VMware bez agenta

Tento článek popisuje některé běžné problémy a konkrétní chyby, se kterými se můžete setkat při replikaci místních virtuálních počítačů VMware pomocí Azure Migrate: metoda bez agenta migrace serveru.

Při replikaci virtuálního počítače VMware pomocí metody replikace bez agenta se data z disků&#39;s virtuálním počítačem (VMDK) replikují na spravované disky repliky ve vašem předplatném Azure. Když se replikace spustí pro virtuální počítač, proběhne počáteční cyklus replikace, při které se replikují úplné kopie disků. Po dokončení počáteční replikace se úlohy přírůstkového replikace pravidelně plánují, aby se přenesly všechny změny, ke kterým došlo od posledního replikačního cyklu.

Občas se může zobrazit selhání replikačních cyklů pro virtuální počítač. K těmto selháním může dojít z důvodů, které se týkají potíží s konfigurací místní sítě, a problémy s back-endu cloudové služby Azure Migrate. V tomto článku budeme:

 - Ukáže vám, jak můžete monitorovat stav replikace a vyřešit chyby.
 - Seznamte se s nejčastějšími chybami replikace a Navrhněte další kroky, které je potřeba opravit.

## <a name="monitor-replication-status-using-the-azure-portal"></a>Monitorování stavu replikace pomocí Azure Portal

K monitorování stavu replikace virtuálních počítačů použijte následující postup:

  1. Přejít na stránku servery v Azure Migrate na Azure Portal.
  2. Kliknutím na replikace serverů na dlaždici migrace serveru přejděte na stránku replikace počítačů.
  3. Zobrazí se seznam replikačních serverů spolu s dalšími informacemi, jako je stav, stav, čas poslední synchronizace atd. Sloupec Health (stav) indikuje aktuální stav replikace virtuálního počítače. Hodnota "kritická" nebo "Warning" ve sloupci Stav obvykle označuje, že předchozí cyklus replikace pro virtuální počítač se nezdařil. Pokud chcete získat další informace, klikněte pravým tlačítkem na virtuální počítač a vyberte podrobnosti o chybě. Stránka Podrobnosti o chybě obsahuje informace o chybě a další podrobnosti o tom, jak řešit potíže. Zobrazí se také odkaz nedávné události, který se dá použít k přechodu na stránku události pro daný virtuální počítač.
  4. Kliknutím na nedávné události zobrazíte předchozí selhání cyklu replikace pro virtuální počítač. Na stránce události vyhledejte nejnovější událost typu "cyklus replikace selhala" nebo "cyklus replikace pro disk" virtuálního počítače "selhal.
  5. Kliknutím na událost pochopíte možné příčiny chyby a doporučené kroky k nápravě. Použijte informace, které jsou k dispozici k řešení potíží, a opravte chybu.
    
## <a name="common-replication-errors"></a>Běžné chyby replikace

Tato část popisuje některé běžné chyby a jejich řešení.

## <a name="key-vault-operation-failed-error-when-trying-to-replicate-vms"></a>Při pokusu o replikaci virtuálních počítačů došlo k chybě operace Key Vault.

**Chyba:** Operace Key Vault se nezdařila. Operace: konfigurace spravovaného účtu úložiště Key Vault: klíč-trezor-Name, účet úložiště: název účtu úložiště se nezdařil s chybou: "

**Chyba:** Operace Key Vault se nezdařila. Operace: generování definice sdíleného přístupového podpisu, Key Vault: klíč-trezor-Name, účet úložiště: název účtu úložiště se nezdařil s chybou: "

![Key Vault](./media/troubleshoot-changed-block-tracking-replication/key-vault.png)

K této chybě obvykle dochází, protože zásady přístupu uživatele pro Key Vault nedávají aktuálně přihlášenému uživateli potřebná oprávnění ke konfiguraci účtů úložiště, které se mají Key Vault spravovat. Pokud chcete vyhledat zásady přístupu uživatele v trezoru klíčů, přejděte na stránku trezoru klíčů na portálu pro Trezor klíčů a vyberte zásady přístupu. 

Když portál vytvoří Trezor klíčů, přidá taky zásadu přístupu uživatele, která uděluje aktuálně přihlášeným uživatelským oprávněním ke konfiguraci účtů úložiště, které se mají Key Vault spravovat. To může selhat ze dvou důvodů

- Přihlášený uživatel je vzdáleným hlavním objektem na zákaznících Azure tenant (předplatné CSP – a přihlášený uživatel je správcem partnera). Alternativním řešením v tomto případě je odstranit Trezor klíčů, odhlaste se z portálu a pak se přihlaste pomocí uživatelského účtu z tenanta Customers (ne vzdáleného objektu zabezpečení) a zkuste operaci zopakovat. Partner CSP má obvykle uživatelský účet ve Azure Active Directory klienta, který mohou používat. Pokud ne, může vytvořit nový uživatelský účet pro sebe ve Azure Active Directory zákazníky, přihlaste se k portálu jako nový uživatel a pak zkuste operaci replikace zopakovat. Účet, který se používá, musí mít oprávnění správce vlastníka nebo přispěvatel + správce přístupu uživatele k účtu ve skupině prostředků (migrace skupiny prostředků projektu).

- Druhý případ, kdy k tomu může dojít, nastane, když se jeden uživatel (uživatel1) pokusil nastavit replikaci zpočátku a narazil na chybu, ale Trezor klíčů už je vytvořený (a zásady přístupu uživatele jsou správně přiřazené tomuto uživateli). Nyní se teď jiný uživatel (uživatel2) pokusí nastavit replikaci, ale operace konfigurace spravovaného účtu úložiště nebo vygenerování definice SAS se nezdařila, protože neexistují žádné zásady přístupu uživatele, které by v trezoru klíčů odpovídaly.

**Řešení**: Pokud chcete tento problém vyřešit, vytvořte zásadu přístupu uživatele pro uživatel2 v trezoru klíčů udělující oprávnění uživatel2 ke konfiguraci spravovaného účtu úložiště a generování definic SAS. Uživatel2 to může udělat z Azure PowerShell pomocí níže uvedených rutin:

$userPrincipalId = $ (Get-AzureRmADUser-UserPrincipalName "user2_email_address"). Účet

Set-AzureRmKeyVaultAccessPolicy-trezor "RegenerateKey"-ObjectId "-ObjectId $userPrincipalId-PermissionsToStorage Get, list, DELETE, set, Update,, getsas, listsas, deletesas, setsas, obnovení, zálohování, obnovení, vyprázdnění


## <a name="disposeartefactstimedout"></a>DisposeArtefactsTimedOut

**ID chyby:** 181008

**Chybová zpráva:** Virtuální počítač: VMName. Chyba: byla zjištěna událost timeout ' DisposeArtefactsTimeout ' ve stavu & ' [' Gateway. Service. StateMachine. SnapshotReplication. SnapshotReplicationEngine + WaitingForArtefactsDisposalPreCycle ' (' WaitingForArtefactsDisposalPreCycle ')] '.

**Možné příčiny:**

Komponenta, která se pokouší replikovat data do Azure, je buď nefunkční, nebo neodpovídá. Mezi možné příčiny patří:

- Služba brány spuštěná v zařízení Azure Migrate nefunguje.
- V rámci služby brány dochází k problémům s připojením k účtu úložiště Service Bus/centra událostí/zařízení.

**Identifikujte přesnou příčinu DisposeArtefactsTimedOut a odpovídající řešení:**

1. Ujistěte se, že je zařízení Azure Migrate v provozu a spuštěné.
2. Ověřte, jestli je na zařízení spuštěná služba brány:
   1.  Přihlaste se k zařízení Azure Migrate pomocí vzdálené plochy a proveďte následující postup.

   2.  Otevřete modul snap-in služby Microsoft Services MMC (spusťte > Services. msc) a ověřte, zda je spuštěna služba Microsoft Azure Gateway. Pokud je služba zastavená nebo nespuštěná, spusťte službu. Případně můžete otevřít příkazový řádek nebo PowerShell a udělat: "net start asrgwy"

3. Ověřte problémy s připojením mezi zařízením Azure Migrate a účtem úložiště zařízení: 

    Po stažení AzCopy do zařízení Azure Migrate spusťte následující příkaz:
    
    _AzCopy lavice https://[účet]. blob. Core. Windows. NET/[kontejner]? VEDE_
    
    **Postup spuštění testu výkonnosti výkonu:**
    
      1. [Stáhnout](../storage/common/storage-use-azcopy-v10.md) AzCopy
        
      2. Vyhledejte účet úložiště zařízení ve skupině prostředků. Účet úložiště má název, který se podobá migrategwsa \* \* \* \* \* \* \* \* \* \* . Toto je hodnota parametru [Account] ve výše uvedeném příkazu.
        
      3. V Azure Portal vyhledejte svůj účet úložiště. Ujistěte se, že předplatné, které používáte pro hledání, je stejné jako předplatné (cílové předplatné), ve kterém je účet úložiště vytvořený. Přejít na kontejnery v části služby BLOB Service. Klikněte na + kontejner a vytvořte kontejner. Ponechte úroveň veřejného přístupu na výchozí vybranou hodnotu.
        
      4. V části Nastavení přejděte na signaturu sdíleného přístupu. Vyberte kontejner v části "povolený typ prostředku". Klikněte na vygenerovat SAS a připojovací řetězec. Zkopírujte hodnotu SAS.
        
      5. Na příkazovém řádku spusťte výše uvedený příkaz nahrazením účtu, kontejneru a SAS hodnotami získanými v krocích 2, 3 a 4 v uvedeném pořadí.
        
      Případně [si stáhněte](https://go.microsoft.com/fwlink/?linkid=2138967) Azure Storage Prozkoumejte zařízení a pokuste se do účtů úložiště nahrát 10 objektů BLOB o velikosti ~ 64 MB. Pokud nedochází k žádnému problému, odeslání by mělo být úspěšné.
        
    **Řešení:** Pokud tento test neproběhne úspěšně, dojde k potížím s&#39;v síti. Zapojte svůj místní síťový tým, aby zkontroloval problémy s připojením. Obvykle se může jednat o některá nastavení brány firewall, která způsobují selhání.
    
4.  Ověřte problémy s připojením mezi zařízením Azure Migrate a Service Bus:

    Tento test ověří, zda zařízení Azure Migrate může komunikovat s back-end cloudové služby Azure Migrate. Zařízení komunikuje s back-endu služby prostřednictvím Service Bus a front zpráv centra událostí. Pokud chcete ověřit připojení ze zařízení k Service Bus, [Stáhněte](https://go.microsoft.com/fwlink/?linkid=2139104) si Service Bus Exploreru, zkuste se připojit k Service Bus zařízení a provést odeslání zprávy nebo zprávy o přijetí. Pokud nedochází k žádnému problému, mělo by to být úspěšné.

    **Postup spuštění testu:**

    1. Zkopírování připojovacího řetězce z Service Bus, které byly vytvořeny v projektu migrace
    2. Otevřete Průzkumníka Service Bus.
    3. Přejít na soubor a pak na připojit
    4. Vložte připojovací řetězec a klikněte na připojit.
    5. Tím se otevře Service Bus názvového prostoru.
    6. V tématu vyberte Snapshot Manager. Klikněte pravým tlačítkem na Snapshot Manager, vyberte přijmout zprávy > vyberte "Náhled" a klikněte na OK.
    7. Pokud je připojení úspěšné, zobrazí se ve výstupu konzoly zpráva [x] přijaté zprávy. Pokud připojení není úspěšné, zobrazí se zpráva informující o tom, že připojení selhalo.
    
    **Řešení:** Pokud tento test neproběhne úspěšně, dojde k problému se sítí. Zapojte svůj místní síťový tým, aby zkontroloval problémy s připojením. Obvykle se může jednat o některá nastavení brány firewall, která způsobují selhání.

5. Problémy s připojením mezi zařízením Azure Migrate a Azure Key Vault:

    Tento test kontroluje problémy s připojením mezi Azure Migrate zařízením a Azure Key Vault. Key Vault se používá ke správě přístupu k účtu úložiště, který se používá pro replikaci.
    
    **Postup pro kontrolu připojení:**
    
    1. Načte identifikátor URI Key Vault ze seznamu prostředků ve skupině prostředků, která odpovídá Azure Migrate projektu.
    
    1. Otevřete PowerShell v zařízení Azure Migrate a spusťte následující příkaz:
    
    _test-NetConnection Key Vault identifikátor URI-P 443_
    
    Tento příkaz se pokusí o připojení TCP a vrátí výstup.
    
     - Ve výstupu vyhledejte pole "_TcpTestSucceeded_". Pokud je hodnota "_true_", nedochází k žádnému problému s připojením mezi Azure Migrate zařízením a Azure Key Vault. Pokud je hodnota false, dojde k problému s připojením.
    
    **Řešení:** Pokud tento test neproběhne úspěšně, dojde k problémům s připojením mezi Azure Migrate zařízením a Azure Key Vault. Zapojte svůj místní síťový tým, aby zkontroloval problémy s připojením. Obvykle se může jednat o některá nastavení brány firewall, která způsobují selhání.
    
## <a name="diskuploadtimedout"></a>DiskUploadTimedOut

**ID chyby:** 1011

**Chybová zpráva:** Nahrávání dat pro disk DiskPath, DiskId virtuálního počítače VMName; VMId nebyl dokončen v očekávaném čase.

Tato chyba obvykle znamená, že Azure Migrate zařízení, které provádí replikaci, se nemůže připojit k Azure Cloud Services nebo že replikace probíhá pomalu, což způsobí vypršení časového limitu replikačního cyklu.

Mezi možné příčiny patří:

- Zařízení Azure Migrate nefunguje.
- Služba Brána replikace není na zařízení spuštěná.
- U služby brány replikace dochází k problémům s připojením k jedné z následujících součástí služby Azure, které se používají pro replikaci: Service Bus/centrum událostí/účet úložiště Azure cache/Azure Key Vault.
- Při pokusu o čtení disku se služba brány omezuje na úrovni vCenter.

**Identifikace hlavní příčiny a řešení problému:**

1. Ujistěte se, že je zařízení Azure Migrate v provozu a spuštěné.
2. Ověřte, jestli je na zařízení spuštěná služba brány:
   1.  Přihlaste se k zařízení Azure Migrate pomocí vzdálené plochy a proveďte následující postup.

   2.  Otevřete modul snap-in služby Microsoft Services MMC (spusťte > Services. msc) a ověřte, zda je spuštěna služba Microsoft Azure Gateway. Pokud je služba zastavená nebo nespuštěná, spusťte službu. Případně můžete otevřít příkazový řádek nebo PowerShell a udělat: "net start asrgwy".


3. **Ověřte problémy s připojením mezi zařízením Azure Migrate a účtem úložiště mezipaměti:** 

    Po stažení AzCopy do zařízení Azure Migrate spusťte následující příkaz:
    
    _AzCopy lavice https://[účet]. blob. Core. Windows. NET/[kontejner]? VEDE_
    
    **Postup spuštění testu výkonnosti výkonu:**
    
      1. [Stáhnout](../storage/common/storage-use-azcopy-v10.md) AzCopy
        
      2. Vyhledejte účet úložiště zařízení ve skupině prostředků. Účet úložiště má název, který se podobá migratelsa \* \* \* \* \* \* \* \* \* \* . Toto je hodnota parametru [Account] ve výše uvedeném příkazu.
        
      3. V Azure Portal vyhledejte svůj účet úložiště. Ujistěte se, že předplatné, které používáte pro hledání, je stejné jako předplatné (cílové předplatné), ve kterém je účet úložiště vytvořený. Přejít na kontejnery v části služby BLOB Service. Klikněte na + kontejner a vytvořte kontejner. Ponechte úroveň veřejného přístupu na výchozí vybranou hodnotu.
        
      4. V části Nastavení přejděte na signaturu sdíleného přístupu. Vyberte kontejner v části "povolený typ prostředku". Klikněte na vygenerovat SAS a připojovací řetězec. Zkopírujte hodnotu SAS.
        
      5. Na příkazovém řádku spusťte výše uvedený příkaz nahrazením účtu, kontejneru a SAS hodnotami získanými v krocích 2, 3 a 4 v uvedeném pořadí.
        
      Případně [si stáhněte](https://go.microsoft.com/fwlink/?linkid=2138967) Azure Storage Prozkoumejte zařízení a pokuste se do účtů úložiště nahrát 10 objektů BLOB o velikosti ~ 64 MB. Pokud nedochází k žádnému problému, odeslání by mělo být úspěšné.
        
    **Řešení:** Pokud tento test neproběhne úspěšně, dojde k potížím s&#39;v síti. Zapojte svůj místní síťový tým, aby zkontroloval problémy s připojením. Obvykle se může jednat o některá nastavení brány firewall, která způsobují selhání.
                
4.  **Problémy s připojením mezi zařízením Azure Migrate a Azure Service Bus:**

    Tento test ověří, jestli Azure Migrate zařízení může komunikovat s back-end cloudové služby Azure Migrate. Zařízení komunikuje s back-endu služby prostřednictvím Service Bus a front zpráv centra událostí. Pokud chcete ověřit připojení ze zařízení k Service Bus, [Stáhněte](https://go.microsoft.com/fwlink/?linkid=2139104) si Service Bus Exploreru, zkuste se připojit k Service Bus zařízení a provést odeslání zprávy nebo zprávy o přijetí. Pokud nedochází k žádnému problému, mělo by to být úspěšné.

    **Postup spuštění testu:**
    
    1. Zkopírujte připojovací řetězec z Service Bus, které byly vytvořeny ve skupině prostředků odpovídající Azure Migrate projektu.
    
    1. Otevřít Service Bus Explorer
    
    1. Přejít na soubor > připojit
    
    1. Vložte připojovací řetězec, který jste zkopírovali v kroku 1, a klikněte na připojit.
    
    1. Tím se otevře Service Bus obor názvů.
    
    1. V tématu obor názvů vyberte Snapshot Manager. Klikněte pravým tlačítkem na Snapshot Manager, vyberte přijmout zprávy > vyberte "Náhled" a klikněte na OK.
    
    Pokud je připojení úspěšné, zobrazí se ve výstupu konzoly zpráva [x] přijaté zprávy. Pokud připojení není úspěšné, zobrazí se zpráva informující o tom, že připojení selhalo.
    
    **Řešení:** Pokud tento test neproběhne úspěšně, dojde k potížím s připojením mezi Azure Migrate zařízením a Service Bus. Zapojte svůj místní síťový tým, aby zkontroloval tyto problémy s připojením. Obvykle se může jednat o některá nastavení brány firewall, která způsobují selhání.
    
 5. **Problémy s připojením mezi zařízením Azure Migrate a Azure Key Vault:**

    Tento test kontroluje problémy s připojením mezi Azure Migrate zařízením a Azure Key Vault. Key Vault se používá ke správě přístupu k účtu úložiště, který se používá pro replikaci.
    
    **Postup pro kontrolu připojení:**
    
    1. Načte identifikátor URI Key Vault ze seznamu prostředků ve skupině prostředků, která odpovídá Azure Migrate projektu.
    
    1. Otevřete PowerShell v zařízení Azure Migrate a spusťte následující příkaz:
    
    _test-NetConnection Key Vault identifikátor URI-P 443_
    
    Tento příkaz se pokusí o připojení TCP a vrátí výstup.
    
    1. Ve výstupu vyhledejte pole "_TcpTestSucceeded_". Pokud je hodnota "_true_", nedochází k žádnému problému s připojením mezi Azure Migrate zařízením a Azure Key Vault. Pokud je hodnota false, dojde k problému s připojením.
    
    **Řešení:** Pokud tento test neproběhne úspěšně, dojde k problémům s připojením mezi Azure Migrate zařízením a Azure Key Vault. Zapojte svůj místní síťový tým, aby zkontroloval problémy s připojením. Obvykle se může jednat o některá nastavení brány firewall, která způsobují selhání.
    
## <a name="encountered-an-error-while-trying-to-fetch-changed-blocks"></a>Při pokusu o načtení změněných bloků došlo k chybě.

Chybová zpráva: při pokusu o načtení bloků změn došlo k chybě.

Metoda replikace bez agenta používá k replikaci dat do Azure službu CBT (ed Block Tracking Technology) VMware. CBT umožňuje nástroji pro migraci serveru sledovat a replikovat pouze bloky, které se od posledního replikačního cyklu změnily. K této chybě dojde, když se pro replikovaný virtuální počítač resetuje funkce Changed Block Tracking nebo když je změněný soubor Changed Block Tracking poškozený.

Tuto chybu lze vyřešit následujícími dvěma způsoby:

- Pokud jste se rozhodli pro automatickou opravu replikace výběrem možnosti Ano při aktivaci replikace virtuálního počítače, nástroj se ho pokusí opravit za vás. Klikněte pravým tlačítkem na virtuální počítač a vyberte opravit replikaci.
- Pokud jste nechtěli vystavit automatickou opravu replikace nebo výše uvedený krok nefungoval za vás, zastavte replikaci pro virtuální počítač, [resetujte změněné sledování bloků](https://go.microsoft.com/fwlink/?linkid=2139203) na virtuálním počítači a pak znovu nakonfigurujte replikaci.

Jeden takový známý problém, který může způsobit obnovení CBT virtuálního počítače na VMware vSphere 5,5 je popsán v tématu [VMware KB 2048201: změněné sledování bloků](https://go.microsoft.com/fwlink/?linkid=2138888) se resetuje po operaci vMotion úložiště v vSphere 5. x. Pokud používáte VMware vSphere 5.5, nezapomeňte provést aktualizace popsané v tomto článku znalostní báze.

Alternativně můžete na virtuálním počítači s využitím VMware PowerCLI použít [resetovat a zrušit sledování bloků VMware.

## <a name="an-internal-error-occurred"></a>Došlo k vnitřní chybě.

Někdy se může vycházet chyba, ke které dochází z důvodu problémů v prostředí VMware a v rozhraní API. Identifikovali jsme následující sadu chyb, protože došlo k chybám souvisejícím s prostředím VMware. Tyto chyby mají pevný formát.

_Chybová zpráva: došlo k vnitřní chybě. [Chybová zpráva]_

Například: chybová zpráva: došlo k vnitřní chybě. [Zjistila se neplatná konfigurace snímku].

V následující části jsou uvedené některé běžně zjištěné chyby VMware a způsob, jakým je můžete zmírnit.

### <a name="error-message-an-internal-error-occurred-server-refused-connection"></a>Chybová zpráva: došlo k vnitřní chybě. [Server odmítl připojení.]

Problém je známým problémem VMware a probíhá v VDDK 6,7. Musíte zastavit službu brány spuštěnou v zařízení Azure Migrate, [Stáhnout aktualizaci z VMware KB](https://go.microsoft.com/fwlink/?linkid=2138889)a restartovat službu brány.

Postup zastavení služby brány:

1. Stiskněte kombinaci kláves Windows + R a otevřete Services. msc. Klikněte na "Microsoft Azure službu brány" a zastavte ji.
2. Případně můžete otevřít příkazový řádek nebo PowerShell a udělat: net stop asrgwy. Zajistěte, abyste počkali, dokud nebudete mít zprávu, že služba přestane běžet.

Postup spuštění služby brány:

1. Stiskněte kombinaci kláves Windows + R a otevřete Services. msc. Klikněte pravým tlačítkem na Microsoft Azure službu brány a spusťte ji.
2. Případně můžete otevřít příkazový řádek nebo PowerShell a udělat: net start asrgwy.

### <a name="error-message-an-internal-error-occurred-an-invalid-snapshot-configuration-was-detected"></a>Chybová zpráva: došlo k vnitřní chybě. [Byla zjištěna neplatná konfigurace snímku. ']

Pokud máte virtuální počítač s více disky, může dojít k této chybě při odebrání disku z virtuálního počítače. Chcete-li tento problém vyřešit, přečtěte si postup v [tomto článku o VMware](https://go.microsoft.com/fwlink/?linkid=2138890).

### <a name="error-message-an-internal-error-occurred-generate-snapshot-hung"></a>Chybová zpráva: došlo k vnitřní chybě. [Generovat zavěšení snímků]

K tomuto problému dochází, když generování snímků přestane reagovat. Když k tomuto problému dojde, můžete vidět, že se úloha vytvoření snímku zastaví na 95% nebo v 99%. Pokud chcete tento problém vyřešit, přečtěte si tento [článek o VMware KB](https://go.microsoft.com/fwlink/?linkid=2138969) .

### <a name="error-message-an-internal-error-occurred-failed-to-consolidate-the-disks-on-vm-_reasons_"></a>Chybová zpráva: došlo k vnitřní chybě. [Nepovedlo se konsolidovat disky na virtuálním počítači _[důvody]_]

Když konsolidujeme disky na konci replikačního cyklu, operace se nezdařila. Postupujte podle pokynů v nástroji [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138970) výběrem vhodného _důvodu_ k vyřešení problému.

K následujícím chybám dojde při selhání operací souvisejících se snímkem VMware – vytvoření, odstranění nebo konsolidace disků. Pokud chcete chyby opravit, postupujte podle pokynů v následující části:

### <a name="error-message-an-internal-error-occurred-another-task-is-already-in-progress"></a>Chybová zpráva: došlo k vnitřní chybě. [Už probíhá jiná úloha.]

K tomuto problému dochází v případě, že na pozadí běží konfliktní úkoly virtuálních počítačů, nebo když vyprší časový limit úlohy v vCenter Server. Postupujte podle řešení uvedeného v následující [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138891).

### <a name="error-message-an-internal-error-occurred-operation-not-allowed-in-current-state"></a>Chybová zpráva: došlo k vnitřní chybě. [Operace není v aktuálním stavu povolena.]

K tomuto problému dochází, když vCenter Server agenti pro správu přestanou fungovat. Pokud chcete tento problém vyřešit, přečtěte si řešení v následující [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138971).

### <a name="error-message-an-internal-error-occurred-snapshot-disk-size-invalid"></a>Chybová zpráva: došlo k vnitřní chybě. [Neplatná velikost disku snímku]

Jedná se o známý problém VMware, ve kterém se velikost disku indikuje snímkem, se změní na nula. Postupujte podle řešení uvedeného v [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138972).

### <a name="error-message-an-internal-error-occurred-memory-allocation-failed-out-of-memory"></a>Chybová zpráva: došlo k vnitřní chybě. [Přidělení paměti se nezdařilo. Nedostatek paměti.]

K tomu dojde v případě, že vyrovnávací paměť hostitele NFC nemá dostatek paměti. Pokud chcete tento problém vyřešit, musíte virtuální počítač (COMPUTE vMotion) přesunout na jiného hostitele, který má bezplatné prostředky.

## <a name="next-steps"></a>Další kroky

Pokračujte v replikaci virtuálních počítačů a proveďte [testovací migraci](./tutorial-migrate-vmware.md#run-a-test-migration).