---
title: Zálohování virtuálních počítačů s technologií Hyper-V pomocí MABS
description: Tento článek obsahuje postupy pro zálohování a obnovení virtuálních počítačů pomocí serveru Microsoft Azure Backup (MABS).
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: a020559229771fff1ecc8fb512a5b2af70240cdd
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509502"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Zálohování virtuálních počítačů s technologií Hyper-V pomocí Azure Backup Server

Tento článek vysvětluje, jak zálohovat virtuální počítače Hyper-V pomocí serveru Microsoft Azure Backup (MABS).

## <a name="supported-scenarios"></a>Podporované scénáře

MABS může zálohovat virtuální počítače, které běží na hostitelských serverech Hyper-V, v následujících scénářích:

- **Virtuální počítače s místním nebo přímým úložiště** – Zálohujete virtuální počítače hostované na samostatných hostitelských serverech Hyper-V, které obsahují místní nebo přímo připojené úložiště. Například: pevný disk, zařízení sítě SAN (Storage Area Network) nebo zařízení úložiště připojené k síti (NAS). Na všech hostitelích musí být nainstalován agent MABS Protection.

- **Virtuální počítače v clusteru s úložištěm CSV** – Zálohujete virtuální počítače hostované v clusteru Hyper-V s úložištěm CSV (sdílený svazek clusteru). Agent MABS Protection je nainstalován na každý uzel clusteru.

## <a name="host-versus-guest-backup"></a>Zálohování v porovnání s hostem

MABS může provádět zálohování virtuálních počítačů Hyper-V na úrovni hostitele nebo hosta. Na úrovni hostitele se agent MABS Protection nainstaluje na hostitelský server nebo cluster Hyper-V a chrání všechny virtuální počítače a datové soubory běžící na tomto hostiteli.   Na úrovni hosta se agent nainstaluje do každého virtuálního počítače a chrání zatížení, které je v tomto počítači k dispozici.

Obě metody mají svá pro a proti:

- Zálohy na úrovni hostitele jsou flexibilní, protože pracují bez ohledu na typ operačního systému spuštěného v hostovaných počítačích a nevyžadují instalaci agenta MABS Protection na každý virtuální počítač. Pokud nasadíte zálohování na úrovni hostitele, můžete obnovit celý virtuální počítač nebo soubory a složky (obnovení na úrovni položek).

- Zálohování na úrovni hosta je užitečné, pokud chcete chránit konkrétní úlohy běžící na virtuálním počítači. Na úrovni hostitele můžete obnovit celý virtuální počítač nebo konkrétní soubory, ale neposkytnete obnovení v kontextu konkrétní aplikace. Pokud chcete například obnovit konkrétní položky SharePointu z zálohovaného virtuálního počítače, měli byste provést zálohování na úrovni hosta tohoto virtuálního počítače. Zálohování na úrovni hosta použijte v případě, že chcete chránit data uložená na průchozích discích. Passthrough umožňuje virtuálnímu počítači přímý přístup k úložnému zařízení a neukládá data virtuálních svazků do souboru VHD.

## <a name="how-the-backup-process-works"></a>Jak funguje proces zálohování

MABS provádí zálohování pomocí VSS následujícím způsobem. Kroky v tomto popisu očíslované, aby byly přehlednější.

1. Synchronizační modul založený na blokování MABS vytvoří počáteční kopii chráněného virtuálního počítače a zajistí, že kopie virtuálního počítače je dokončena a konzistentní.

2. Po provedení a ověření počáteční kopie MABS použije zapisovač VSS technologie Hyper-V k zachycení záloh. Zapisovač VSS poskytuje sadu diskových bloků s konzistentními daty, které jsou synchronizované se serverem MABS. Tento přístup poskytuje výhodu "úplného zálohování" se serverem MABS při minimalizaci zálohovaných dat, která je nutné přenést přes síť.

3. Agent MABS Protection na serveru, na kterém běží technologie Hyper-V, používá existující rozhraní API technologie Hyper-V k určení, jestli chráněný virtuální počítač podporuje také službu VSS.

   - Jestliže virtuální počítač odpovídá požadavkům zálohování online a je v něm instalována komponenta integračních služeb technologie Hyper-V, potom zapisovač VSS technologie Hyper-V rekurzivně předává požadavek služby VSS do všech procesů podporujících službu VSS ve virtuálním počítači. Tato operace probíhá, aniž by byl na virtuálním počítači nainstalován agent MABS Protection. Pomocí tohoto rekurzivního požadavku služby VSS může zapisovač VSS technologie Hyper-V zajistit synchronizaci operací zápisu na disk, aby se snímek služby VSS pořídil bez ztráty dat.

     Komponenta integračních služeb technologie Hyper-V vyvolá zapisovač VSS technologie Hyper-V ve službě Stínová kopie svazku (VSS) ve virtuálních počítačích, aby bylo zajištěno, že jejich aplikační data budou v konzistentním stavu.

   - Pokud virtuální počítač nesplňuje požadavky online zálohování, MABS pomocí rozhraní API technologie Hyper-V automaticky pozastaví virtuální počítač před tím, než zachytí datové soubory.

4. Jakmile se počáteční základní kopie virtuálního počítače synchronizuje se serverem MABS, budou všechny změny provedené v prostředcích virtuálního počítače zachyceny v novém bodu obnovení. Bod obnovení představuje konzistentní stav virtuálního stroje v určitou dobu. K zachycení bodu obnovení musí dojít alespoň jednou denně. Při vytvoření nového bodu obnovení používá MABS replikaci na úrovni bloku ve spojení s zapisovačem VSS technologie Hyper-V k určení, které bloky se na serveru s technologií Hyper-V po vytvoření posledního bodu obnovení změnily. Tyto datové bloky jsou pak přeneseny na server MABS a jsou aplikovány na repliku chráněných dat.

5. Server MABS používá službu VSS na svazcích, které hostují data pro obnovení, aby bylo k dispozici více stínových kopií. Každá z těchto stínových kopií poskytuje samostatné obnovení. Body obnovení služby VSS jsou uloženy na serveru MABS. Dočasná kopie vytvořená na serveru se spuštěnou technologií Hyper-V je uložena pouze po dobu trvání synchronizace MABS.

>[!NOTE]
>
>Od verze Windows Server 2016 jsou virtuální pevné disky technologie Hyper-V integrované sledování změn známé jako odolné sledování změn (RCT). MABS používá RCT (nativní sledování změn v technologii Hyper-V), což snižuje nutnost časově náročné kontroly konzistence ve scénářích, jako jsou například chyby virtuálních počítačů. RCT nabízí lepší odolnost než sledování změn poskytované zálohami založenými na snímcích služby Stínová kopie svazku (VSS). MABS V3 optimalizuje využití sítě a úložiště tím, že během jakýchkoli kontrol konzistence přenáší jenom změněná data.

## <a name="backup-prerequisites"></a>Požadavky pro zálohování

Toto jsou požadavky pro zálohování virtuálních počítačů s technologií Hyper-V pomocí nástroje MABS:

|Požadavek|Podrobnosti|
|------------|-------|
|Požadavky na MABS|– Pokud chcete provést obnovení na úrovni položky pro virtuální počítače (obnovení souborů, složek, svazků), budete muset na server MABS nainstalovat roli Hyper-V.  Pokud chcete pouze obnovit virtuální počítač, nikoli úroveň položky, není tato role nutná.<br />– Můžete chránit až 800 virtuálních počítačů 100 GB na jednom serveru MABS a povolíte více serverů MABS podporujících větší clustery.<br />– MABS vyloučí stránkovací soubor z přírůstkového zálohování pro zlepšení výkonu zálohování virtuálních počítačů.<br />-MABS může zálohovat server nebo cluster Hyper-V ve stejné doméně jako server MABS nebo v podřízené nebo důvěryhodné doméně. Pokud chcete vytvořit zálohu technologie Hyper-V v pracovní skupině nebo v nedůvěryhodné doméně, bude nutné nastavit ověřování. Pro jeden server Hyper-V můžete použít ověřování protokolem NTLM nebo ověření certifikátu. V případě clusteru můžete použít pouze ověřování certifikátů.<br />– Na úrovni hostitele se nedají zálohovat data virtuálního počítače z průchozích disků. V tomto scénáři doporučujeme použít zálohování na úrovni hostitele k zálohování souborů VHD a zálohování na úrovni hosta k zálohování dalších dat, která nejsou na hostiteli viditelná.<br />   – Můžete zálohovat virtuální počítače uložené na svazcích s odstraněnými duplicitními daty.|
|Požadavky virtuálních počítačů Hyper-V|-Verze integračních komponent, které běží na virtuálním počítači, by měla být stejná jako verze hostitele Hyper-V. <br />– Pro každou zálohu virtuálního počítače budete potřebovat volné místo na svazku, který je hostitelem souborů virtuálního pevného disku, abyste zajistili technologii Hyper-V dost místa pro rozdílové porovnávání disků (AVHD) během zálohování. Prostor musí být minimálně roven času vypočítanému jako **Počáteční velikost disku\*Klidové vytížení\*Zálohování**. Pokud spouštíte více záloh v clusteru, budete potřebovat dostatečnou kapacitu úložiště (použijte tento výpočet) pro AVHD na všech virtuálních počítačích.<br />– Chcete-li zálohovat virtuální počítače nacházející se na hostitelských serverech technologie Hyper-V se systémem Windows Server 2012 R2, musí mít virtuální počítač zadaný řadič SCSI, a to i v případě, že není připojen k žádnému. (V online zálohování Windows Serveru 2012 R2 Hostitel Hyper-V připojí nový virtuální pevný disk ve virtuálním počítači a později ho odpojí. To může podporovat jenom řadič SCSI, a proto se vyžaduje pro online zálohování virtuálního počítače.  Bez tohoto nastavení se při pokusu o zálohování virtuálního počítače vydá událost s ID 10103.)|
|Požadované součásti systému Linux|– Virtuální počítače se systémem Linux můžete zálohovat pomocí MABS. Podporovány jsou pouze snímky konzistentních souborů.|
|Zálohování virtuálních počítačů s úložištěm CSV|– K úložišti CSV nainstalujte na server Hyper-V zprostředkovatele hardwaru služby Stínová kopie svazku (VSS). Kontaktujte dodavatele sítě SAN pro poskytovatele hardwaru VSS.<br />– Pokud se jeden uzel v clusteru CSV neočekávaně ukončí, MABS provede kontrolu konzistence u virtuálních počítačů, které v tomto uzlu běžely.<br />– Pokud potřebujete restartovat server Hyper-V, který má na clusteru CSV povolený nástroj BitLocker Drive Encryption, je potřeba, abyste spustili kontrolu konzistence virtuálních počítačů Hyper-V.|
|Zálohování virtuálních počítačů s úložištěm SMB|– Zapnutím automatického připojení na serveru, na kterém běží technologie Hyper-V, povolíte ochranu virtuálního počítače.<br />   – Zakažte funkci TCP Chimney Offload.<br />– Zajistěte, aby všechny účty machine$ s technologií Hyper-V měly úplná oprávnění v konkrétních vzdálených sdílených složkách souborů SMB.<br />– Ujistěte se, že cesta k souboru pro všechny součásti virtuálního počítače během obnovení do alternativního umístění je kratší než 260 znaků. Pokud ne, obnovení může být úspěšné, ale technologie Hyper-V nebude moci připojit virtuální počítač.<br />– Následující scénáře nejsou podporovány:<br />     Nasazení, ve kterých jsou některé součásti virtuálního počítače na místních svazcích a některé součásti na vzdálených svazcích; adresa IPv4 nebo IPv6 pro souborový server umístění úložiště a obnovení virtuálního počítače do počítače, který používá vzdálené sdílené složky protokolu SMB.<br />– Službu agenta VSS souborového serveru budete muset povolit na každém serveru SMB – přidat ho v části **Přidat role a funkce**  >  **Vybrat role serveru** souborové služby a služba úložiště Souborová služba souborového  >    >    >    >  **serveru služba agenta VSS**.|

## <a name="back-up-virtual-machines"></a>Zálohování virtuálních počítačů

1. Nastavte [Server MABS](backup-azure-microsoft-azure-backup.md) a [úložiště](backup-mabs-add-storage.md). Pro nastavení úložiště postupujte podle těchto pokynů pro jeho kapacitu.
   - Průměrná velikost virtuálního počítače: 100 GB
   - Počet virtuálních počítačů na server MABS-800
   - Celková velikost 800 virtuálních počítačů: 80 TB
   - Požadované místo pro úložiště pro zálohování: 80 TB

2. Nastavte agenta ochrany MABS Protection na serverech Hyper-V nebo na uzlech clusteru Hyper-V. Pokud provádíte zálohování na úrovni hosta, nainstalujete agenta na virtuální počítače, které chcete zálohovat na úrovni hosta.

3. V konzole pro správu MABS vyberte **ochrana**  >  **vytvořit skupinu ochrany** a otevřete průvodce **vytvořením nové skupiny ochrany** .

4. Na stránce **Vybrat členy skupiny** vyberte virtuální počítače, které chcete chránit, a to z hostitelských serverů Hyper-V, na kterých jsou umístěny. Všechny virtuální počítače se stejnými zásadami ochrany doporučujeme dát do jedné skupiny ochrany. Pokud chcete šetřit místem, zapněte kolokaci. Kolokace umožňuje najít data z různých skupin ochrany na stejném disku nebo pásce, aby více zdrojů dat mělo jen jednu repliku a jeden svazek bodu obnovení.

5. Na stránce **Vyberte způsob ochrany dat** zadejte název skupiny ochrany. Pokud chcete zálohovat data na Azure pomocí služby Zálohování Azure, vyberte **Chci krátkodobou ochranu pomocí disku** a potom **Chci online ochranu** .

6. V oblasti **Zadejte Short-Term cíle**  >  **uchování** určete, jak dlouho chcete uchovat data na disku. V části **četnost synchronizací** určete, jak často se má spouštět přírůstkové zálohování dat. Nebo můžete místo intervalu přírůstkového zálohování vybrat možnost **Těsně před bodem obnovení**. Pokud je toto nastavení povoleno, MABS spustí expresní úplné zálohování těsně před každým naplánovaným bodem obnovení.

    > [!NOTE]
    >
    >Pokud chráníte úlohy aplikace a ta podporuje přírůstkové zálohování, vytváří se body obnovení na základě nastavení četnosti synchronizací. Pokud ne, MABS spustí expresní úplné zálohování místo přírůstkového zálohování a vytvoří body obnovení v souladu s plánem expresního zálohování.

7. Na stránce **zkontrolovat přidělení disku** zkontrolujte přidělené místo na disku fondu úložiště pro skupinu ochrany.

   **Celková velikost dat** je velikost dat, která chcete zálohovat, a **místo na disku, které se má zřídit v MABS** , je místo, které MABS pro skupinu ochrany doporučuje. MABS vybere ideální záložní svazek na základě nastavení. Možnosti záložního svazku ale můžete upravit v části **Podrobnosti přidělení disku**. V rozevírací nabídce vyberte požadované úložiště pro úlohy. Úpravy, které provedete, změní hodnoty v polích **Celková velikost úložiště** a **Volný úložný prostor** v podokně **Dostupný úložný prostor na disku**. Za nezřízené místo je množství MABS úložiště, které vám nabídne přidání do svazku, aby bylo možné v budoucnu pokračovat v zálohování.

8. Na stránce **Vyberte způsob vytvoření repliky** určete, jak se bude provádět počáteční replikace dat ve skupině ochrany. Pokud zvolíte **automatickou replikaci přes síť**, doporučujeme vám vybrat dobu mimo špičku. Pro velké objemy dat nebo méně, než jsou optimální síťové podmínky, zvažte možnost **ručního** výběru, který vyžaduje replikaci dat offline pomocí vyměnitelného média.

9. Na stránce **Možnosti kontroly konzistence** vyberte, jak chcete automatizovat kontroly konzistence. Spuštění kontroly můžete povolit jenom pro případ, že začnou být nekonzistentní data repliky, nebo podle plánu. Pokud automatickou kontrolu konzistence nechcete konfigurovat, můžete kdykoliv spustit ruční kontrolu tak, že pravým tlačítkem kliknete na skupinu ochrany a vyberete **Provést kontrolu konzistence**.

    Po vytvoření skupiny ochrany dojde k počáteční replikaci dat v souladu s vámi zvoleným způsobem. Všechna zálohování po počáteční replikaci probíhají podle nastavení skupiny ochrany. Pokud potřebujete obnovit zálohovaná data, vezměte na vědomí následující:

## <a name="back-up-replica-virtual-machines"></a>Zálohování virtuálních počítačů repliky

Pokud je MABS spuštěný v systému Windows Server 2012 R2 nebo novějším, můžete zálohovat virtuální počítače repliky. To je užitečné z několika důvodů:

**Sníží to dopad zálohování na běžící úlohu** – zálohování virtuálního počítače při vytváření snímku totiž trochu zvyšuje zatížení. Přesměrováním procesu zálohování na sekundární vzdálený web je spuštěná úloha již ovlivněna operací zálohování. To se vztahuje pouze na nasazení, kde se záložní kopie ukládají na vzdálený web. Například můžete provádět denní zálohy a ukládat data místně pro zajištění rychlých časů obnovení, ale provádět měsíční nebo čtvrtletně zálohy z virtuálních počítačů repliky uložené pro dlouhodobé ukládání.

**Šetří šířku pásma** – v typických nasazeních vzdálené větve pobočky nebo sídla společnosti potřebujete zajistit odpovídající šířku přenosového pásma k přenosu zálohovaných dat mezi servery. Pokud vedle strategie pro zálohu dat vytvoříte také strategii pro replikaci nebo pro převzetí služeb při selhání, můžete tím snížit objem redundantních dat posílaných prostřednictvím sítě. Zálohováním dat virtuálního počítače repliky místo primárního počítače ušetříte režijní náklady posílání zálohovaných dat po síti.

**Umožňuje to zálohování hostitele** – jako server replik můžete použít hostované datové centrum, aniž byste potřebovali sekundární datové centrum. V takovém případě bude SLA hostitele vyžadovat konzistentní zálohování virtuálních počítačů replik.

Virtuální počítač repliky je do převzetí služeb při selhání vypnutý, takže služba Stínová kopie svazku (VSS) nemůže zaručit, že takový počítač zazálohuje konzistentně vzhledem k aplikacím. Zálohování virtuálního počítače repliky bude proto jenom v souladu s chybami. Pokud ani konzistence vzhledem k selháním nemůže být zaručena, zálohování se nepovede. To se může stát v několika situacích:

- Virtuální počítač repliky není v pořádku a je v kritickém stavu.

- Virtuální počítač repliky se opětovně synchronizuje (je ve stavu Probíhá nová synchronizace nebo Vyžadována opětovná synchronizace).

- Probíhá počáteční replikace mezi primárním a sekundárním webem nebo čeká na dokončení pro virtuální počítač.

- protokoly. hrl se používají pro virtuální počítač repliky nebo se předchozí akce pro použití protokolů. hrl na virtuálním disku nezdařila, nebo byla zrušena nebo přerušena.

- Probíhá migrace virtuálního počítače repliky nebo převzetí služeb při jeho selhání.

## <a name="recover-backed-up-virtual-machines"></a>Obnovení zálohovaných virtuálních počítačů

Když chcete obnovit zálohovaný virtuální počítač, pomocí Průvodce obnovením vyberte daný virtuální počítač a konkrétní bod obnovení. Průvodce obnovením otevřete a virtuální počítač obnovíte takto:

1. V konzole pro správu MABS zadejte název virtuálního počítače, nebo rozbalte seznam chráněných položek a vyberte virtuální počítač, který chcete obnovit.

2. V podokně **body obnovení pro** v kalendáři vyberte libovolné datum, aby se zobrazily dostupné body obnovení. Pak v podokně **Cesta** vyberte bod obnovení, který chcete použít v Průvodci obnovením.

3. V nabídce **Akce** vyberte **obnovit** a otevřete tak Průvodce obnovením.

    Vybraný virtuální počítač a bod obnovení se zobrazí v obrazovce **Revidovat výběr obnovení**. Vyberte **Další**.

4. Na obrazovce **Vybrat typ obnovení** vyberte, kam chcete data obnovit, a pak vyberte **Další**.

    - **Obnovit na původní instanci**: při obnovení na původní instanci se odstraní původní virtuální pevný disk a všechny přidružené kontrolní body. MABS obnoví virtuální pevný disk a další konfigurační soubory do původního umístění pomocí zapisovače VSS technologie Hyper-V. Na konci procesu obnovení jsou virtuální počítače stále vysoce dostupné.
        Pro obnovení musí být dostupná skupina prostředků. Pokud není dostupná, proveďte obnovení do alternativního umístění a pak změňte virtuální počítač na vysoce dostupný.

    - **Obnovit jako virtuální počítač na libovolného hostitele**: MABS podporuje obnovení do alternativního umístění (ALR), které poskytuje bezproblémové obnovení chráněného virtuálního počítače s technologií Hyper-v na jiného hostitele Hyper-v, nezávisle na architektuře procesoru. Virtuální počítače Hyper-V, které jsou obnoveny do uzlu clusteru, nebudou vysoce dostupné. Pokud zvolíte tuto možnost, Průvodce obnovením vám nabídne další obrazovku, kde identifikujete cíl a cílovou cestu.
    
        >[!NOTE]
        >Pokud vyberete původního hostitele, bude chování stejné jako **obnovení do původní instance**. Původní virtuální pevný disk a všechny přidružené kontrolní body se odstraní.

    - **Kopírovat do síťové složky**: MABS podporuje obnovení na úrovni položek (ilr), které umožňuje provádět obnovení na úrovni položek souborů, složek, svazků a virtuálních pevných disků (VHD) ze záloh virtuálních počítačů Hyper-V na úrovni hostitele do sdílené síťové složky nebo svazku na chráněném serveru MABS. K provedení obnovení na úrovni položek není nutné instalovat agenta MABS Protection v rámci hosta. Pokud zvolíte tuto možnost, Průvodce obnovením vám nabídne další obrazovku, kde identifikujete cíl a cílovou cestu.

5. V části **zadat možnosti obnovení** nakonfigurujte možnosti obnovení a vyberte **Další**:

    - Pokud obnovujete virtuální počítač přes malou šířku pásma, vyberte **Upravit** a povolte **omezení využití šířky pásma sítě**. Až zapnete omezení využití šířky pásma, můžete zadat šířku písma sítě, kterou chcete mít k dispozici, a čas, kdy chcete, aby byla dostupná.
    - Pokud jste nakonfigurovali síť, vyberte možnost **Povolit obnovení na základě San pomocí snímků hardwaru** .
    - Pokud chcete po dokončení procesu obnovení dostat e-mailové upozornění, vyberte **Odeslat e-mail po dokončení obnovení** a pak zadejte e-mailovou adresu.

6. Na obrazovce Souhrn zkontrolujte, jestli jsou všechny podrobnosti správné. Pokud nejsou podrobnosti správné nebo chcete provést změnu, vyberte **zpět**. Pokud jste s nastavením spokojeni, vyberte **obnovit** a spusťte proces obnovení.

7. Obrazovka **Stav obnovení Status** poskytuje informace o úloze obnovení.

## <a name="next-steps"></a>Další kroky

[Obnovení dat z Azure Backup Serveru](./backup-azure-alternate-dpm-server.md)
