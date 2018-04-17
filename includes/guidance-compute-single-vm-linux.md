Tento článek popisuje sadu osvědčených postupů pro spuštění linuxového virtuálního počítače v Azure, s důrazem na škálovatelnost, dostupnost, možnosti správy a zabezpečení. Azure podporuje spouštění různých oblíbených linuxových distribucí, mezi které patří CentOS, Debian, Red Hat Enterprise, Ubuntu a FreeBSD. Další informace najdete v tématu [Azure a Linux][azure-linux].

> [!NOTE]
> Azure má dva různé modely nasazení: [Resource Manager][resource-manager-overview] a klasický model. Tento článek je zaměřený na Resource Manager, který společnost Microsoft doporučuje pro nová nasazení.
> 
> 

Nedoporučujeme používat pro nejdůležitější úlohy jenom jeden virtuální počítač, protože tak vzniká jediný bod selhání. Pro zajištění vyšší dostupnosti nasaďte několik virtuálních počítačů ve [skupině dostupnosti][availability-set]. Další informace najdete v tématu [Spuštění několika virtuálních počítačů v Azure][multi-vm]. 

## <a name="architecture-diagram"></a>Diagram architektury
Zřizování virtuálních počítačů v Azure zahrnuje přesunutí více částí než jenom samotných virtuálních počítačů. Je potřeba vzít v úvahu také výpočetní prostředky, síť a úložiště.

> Dokument ve Visiu, který obsahuje tento diagram architektury, je dostupný ke stažení na webu [Microsoft Download Center][visio-download]. Najdete ho na stránce s označením Compute – single VM.
> 
> 

![[0]][0]

* **Skupina prostředků:** [*Skupina prostředků*][resource-manager-overview] je kontejner, který obsahuje související prostředky. Vytvořte skupinu prostředků, do které se uloží prostředky pro tento virtuální počítač.
* **Virtuální počítač:** Virtuální počítač můžete zřídit ze seznamu publikovaných imagí nebo ze souboru virtuálního pevného disku (VHD), který nahrajete do Azure Blob Storage.
* **Disk s operačním systémem:** Disk s operačním systémem je a VHD uložený ve službě [Azure Storage][azure-storage]. To znamená, že jeho obsah se zachová, i když se hostitelský počítač vypne. Disk s operačním systémem je `/dev/sda1`.
* **Dočasný disk:** Virtuální počítač se vytvoří s dočasným diskem. Tento disk je uložený na fyzické jednotce hostitelského počítače. *Neukládá se* v Azure Storage a při restartování nebo jiných událostech životního cyklu virtuálního počítače může dojít k jeho odstranění. Tento disk používejte jenom pro dočasná data, jako jsou stránkovací nebo odkládací soubory. Dočasný disk je `/dev/sdb1` a je připojený k `/mnt/resource` nebo `/mnt`.
* **Datové disky:** [Datový disk][data-disk] je trvalý virtuální pevný disk, který se používá pro data aplikací. Datové disky se stejně jako disk s operačním systémem ukládají ve službě Azure Storage.
* **Virtuální síť a podsíť:** Každý virtuální počítač v Azure je nasazený do virtuální sítě, která se dál dělí na podsítě.
* **Veřejná IP adresa:** Veřejná IP adresa je potřeba ke komunikaci s virtuálním počítačem, třeba přes protokol SSH.
* **Síťové rozhraní (NIC):** Síťové rozhraní umožňuje virtuálnímu počítači komunikovat s virtuální sítí.
* **Skupina zabezpečení sítě (NSG):** [NSG][nsg] umožňuje povolit nebo zamítnout síťový provoz do podsítě. NSG můžete přidružit k jednotlivým rozhraním NIC nebo k podsíti. Pokud NSG přidružíte k podsíti, vztahují se pravidla NSG na všechny virtuální počítače v této podsíti.
* **Diagnostika:** Protokolování diagnostiky má pro správu a řešení potíží s virtuálními počítači zásadní význam.

## <a name="recommendations"></a>Doporučení

Následující doporučení platí pro většinu scénářů. Pokud nemáte konkrétní požadavek, který by těmto doporučením nedopovídal, postupujte podle nich. 

### <a name="vm-recommendations"></a>Doporučení pro virtuální počítače

Azure nabízí mnoho různých velikostí virtuálních počítačů, ale my doporučujeme řady GS a DS, a to proto, že tyto velikosti počítačů podporují [Premium Storage][premium-storage]. Pokud nemáte speciální úlohy, jako je třeba vysokovýkonné výpočetní prostředí (HPC), vyberte jednu z těchto velikosti počítačů. Podrobnosti najdete v tématu věnovaném [velikostem virtuálních počítačů][virtual-machine-sizes].

Pokud přesouváte existující úlohy do Azure, začněte s velikostí virtuálního počítače, která nejvíce odpovídá vašim místním serverům. Potom změřte výkon vaší aktuální úlohy s ohledem na využití procesoru, paměti a diskových vstupně-výstupních operací za sekundu (IOPS) a v případě potřeby velikost upravte. Pokud budete pro virtuální počítač potřebovat několik síťových rozhraní, uvědomte si, že maximální počet síťových adaptérů závisí na [velikosti virtuálního počítače][vm-size-tables].

Při zřizování virtuálního počítače a dalších prostředků je nutné zadat oblast. Obecně volte oblast co nejblíž vašim interním uživatelům nebo zákazníkům. Některé velikosti virtuálních počítačů ale nejsou dostupné ve všech oblastech. Podrobnosti najdete v tématu [Služby v jednotlivých oblastech][services-by-region]. Pro zobrazení seznamu velikostí virtuálních počítačů dostupných v příslušné dané oblasti spusťte následující příkaz rozhraní příkazového řádku Azure (CLI):

```
azure vm sizes --location <location>
```

Informace o volbě publikované image virtuálního počítače najdete v tématu věnovaném [výběru image linuxového virtuálního počítače pomocí rozhraní příkazového řádku Azure][select-vm-image].

### <a name="disk-and-storage-recommendations"></a>Doporučení pro disk a úložiště

Pro zajištění nejlepšího výkonu diskových operací doporučujeme použít službu [Premium Storage][premium-storage], která ukládá data na discích SSD (solid-state drive). Náklady závisí na velikosti zřízeného disku. Na velikosti disku závisí také vstupně-výstupní operace za sekundu a propustnost (to znamená rychlost přenosu dat). Proto při zřizování disku zvažte všechny tři faktory (kapacita, IOPS a propustnost). 

Pro každý virtuální počítač vytvořte samostatné účty úložiště Azure pro uložení virtuálních pevných disků (VHD). Nenarazíte tak na limity IOPS pro účty úložiště. 

Přidejte jeden nebo několik datových disků. Když vytvoříte virtuální pevný disk, je neformátovaný. Přihlaste se k virtuálnímu počítači a disk naformátujte. V prostředí Linuxu se datové disky zobrazují jako `/dev/sdc`, `/dev/sdd` atd. Spuštěním příkazu `lsblk` můžete zobrazit seznam blokových zařízení, včetně těchto disků. Pokud chcete použít datový disk, vytvořte oddíl a souborový systém a potom disk připojte. Příklad:

```bat
# Create a partition.
sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

Pokud máte velký počet datových disků, nezapomínejte na celkové limity V/V účtu úložiště. Další informace najdete v tématu věnovaném [omezením disku virtuálního počítače][vm-disk-limits].

Když přidáte datový disk, přiřadí se mu ID logické jednotky (LUN). Volitelně můžete LUN ID zadat, třeba když vyměňujete disk a chcete zachovat stejné LUN ID nebo když máte aplikaci, která hledá konkrétní LUN ID. Nezapomeňte ale, že hodnota LUN ID musí být pro každý disk jedinečná.

Možná budete chtít plánovač vstupně-výstupních operací změnit kvůli optimalizaci výkonu na jednotkách SSD, protože disky pro virtuální počítače s účty Premium Storage jsou právě disky SSD. Běžně se pro SSD doporučuje používat plánovač NOOP, ale k monitorování výkonu V/V disku pro konkrétní úlohu měli byste použít nástroj, jako je [iostat].

K zajištění nejlepšího výkonu vytvořte samostatný účet úložiště pro uložení diagnostických protokolů. Pro diagnostické protokoly stačí standardní účet místně redundantního úložiště (LRS).

### <a name="network-recommendations"></a>Doporučení pro síť

Veřejná IP adresa může být dynamická nebo statická. Ve výchozím nastavení je dynamická.

* Pokud potřebujete pevnou IP adresu, která se nemění, třeba pokud potřebujete vytvořit záznam typu A v DNS nebo přidat IP adresu do seznamu bezpečných adres, vyhraďte [statickou IP adresu][static-ip].
* Pro IP adresu můžete také vytvořit plně kvalifikovaný název domény (FQDN). Potom můžete v DNS zaregistrovat [záznam CNAME][cname-record], který na tento plně kvalifikovaný název odkazuje. Další informace najdete v tématu [Vytvoření plně kvalifikovaného názvu domény (FQDN) na webu Azure Portal][fqdn].

Každá skupina zabezpečení sítě obsahuje sadu [výchozích pravidel][nsg-default-rules], včetně pravidla, které blokuje veškerou příchozí internetovou komunikaci. Výchozí pravidla nejde odstranit, ale ostatní pravidla je mohou potlačit. Pokud chcete povolit internetovou komunikaci, vytvořte pravidla, která povolují příchozí provoz na konkrétních portech, třeba na portu 80 pro HTTP.  

Pokud chcete povolit SSH, přidejte do skupiny NSG pravidlo, které povoluje příchozí provoz na TCP port 22.

## <a name="scalability-considerations"></a>Aspekty zabezpečení

Pokud potřebujete vertikálně navýšit nebo snížit kapacitu, [změňte velikost virtuálního počítače][vm-resize]. 

Pokud potřebujete kapacitu navýšit horizontálně, dejte dva nebo víc virtuálních počítačů do skupiny dostupnosti za nástrojem pro vyrovnávání zatížení. Další informace najdete v tématu věnovaném [spuštění několika virtuálních počítačů v Azure][multi-vm].

## <a name="availability-considerations"></a>Aspekty dostupnosti

Pro zajištění vyšší dostupnosti nasaďte několik virtuálních počítačů ve skupině dostupnosti. Výsledkem je i vyšší [smlouva o úrovni služeb][vm-sla] (SLA). 

Váš virtuální počítač může ovlivňovat [plánovaná][planned-maintenance] nebo [neplánovaná údržba][manage-vm-availability]. Ke zjištění toho, jestli restartování virtuálního počítače způsobila plánovaná údržba, můžete použít [protokoly restartování virtuálního počítače][reboot-logs].

Disky VHD se ukládají v [úložišti Azure][azure-storage] a úložiště Azure se z důvodů zajištění dostupnosti a odolnosti replikuje.

K zajištění ochrany před náhodnou ztrátou dat během běžného provozu (třeba kvůli chybě uživatele) byste měli také implementovat zálohy k určitému časovému okamžiku, a to pomocí [snímků objektů blob][blob-snapshot] nebo jiného nástroje.

## <a name="manageability-considerations"></a>Aspekty správy

**Skupiny prostředků:** Do jedné [skupiny prostředků][resource-manager-overview] byste měli umístit vysoce provázané prostředky, které mají stejný životní cyklus. Skupiny prostředků umožňují nasadit a monitorovat prostředky jako skupinu a seskupit fakturační náklady podle skupin prostředků. Prostředky můžete také odstranit jako sadu, což je velmi užitečné pro testovací nasazení. Dejte prostředkům smysluplné názvy. Usnadní to vyhledání konkrétních prostředků a pochopení jejich rolí. Další informace najdete v tématu [Doporučené zásady vytváření názvů pro prostředky Azure][naming conventions].

**SSH:** Než vytvoříte virtuální počítač s Linuxem, vygenerujte pár klíčů (veřejný a privátní) pomocí 2048bitového algoritmu RSA. Soubor veřejného klíče potom použijte při vytváření virtuálního počítače. Další informace najdete v tématu [Jak použít SSH se systémy Linux a Mac v Azure][ssh-linux].

**Diagnostika virtuálních počítačů:** Povolte monitorování a diagnostiku, včetně základních metrik stavu, diagnostických protokolů infrastruktury a [diagnostiky spouštění][boot-diagnostics]. Diagnostika spouštění vám pomůžete diagnostikovat chyby spouštění, pokud se virtuální počítač dostane do nespustitelného stavu. Další informace najdete v tématu [Povolení monitorování a diagnostiky][enable-monitoring].  

Diagnostika se dá povolit pomocí těchto příkazů rozhraní příkazového řádku:

```
azure vm enable-diag <resource-group> <vm-name>
```

**Zastavení virtuálního počítače:** Azure rozlišuje mezi tím, když je virtuální počítač zastavený a když má zrušené přidělení. Když se virtuální počítač v zastaveném stavu, fakturuje se vám, ale když má zrušené přidělení, tak ne.

K uvolnění virtuálního počítače použijte tento příkaz rozhraní příkazového řádku:

```
azure vm deallocate <resource-group> <vm-name>
```

Virtuální počítač můžete uvolnit pomocí tlačítka **Stop** na webu Azure Portal. Pokud ale virtuální počítač vypnete pomocí operačního systému, když jste přihlášení, zastaví se, ale *neuvolní*, takže se vám bude nadále účtovat.

**Odstranění virtuálního počítače:** Pokud odstraníte virtuální počítač, virtuální pevné disky se odstraní. To znamená, že virtuální počítač můžete bezpečně odstranit bez obav ze ztráty dat. Bude se vám ale účtovat poplatek za úložiště. Pokud chcete odstranit virtuální pevný disku, odstraňte příslušný soubor z [úložiště objektů blob][blob-storage].

Pokud chcete zabránit neúmyslnému odstranění, použijte [zámek prostředku][resource-lock] a uzamkněte celou skupinu prostředků nebo jenom vybrané prostředky, jako je třeba virtuální počítač. 

## <a name="security-considerations"></a>Aspekty zabezpečení

K automatizaci aktualizací operačního systému použijte rozšíření virtuálního počítače [OSPatching]. Toto rozšíření nainstalujte při zřizování virtuálního počítače. Můžete zadat, jak často se mají instalovat opravy a jestli se po provedení oprav má provést restartování.

Pro řízení přístupu k prostředkům Azure, které nasadíte, použijte [řízení přístupu na základě role][rbac] (RBAC). RBAC umožňuje přiřazovat autorizační role jednotlivým členům vaše týmu DevOps. Třeba role čtenáře může zobrazovat prostředky Azure, ale nemůže je vytvářet, spravovat ani odstraňovat. Některé role jsou specifické pro konkrétní typy prostředků Azure. Třeba role Přispěvatel virtuálních počítačů může restartovat nebo zrušit přidělení virtuálního počítače, resetovat heslo správce, vytvořit virtuální počítače a tak dále. Mezi další [předdefinované role RBAC][rbac-roles], které by mohly být pro tuto referenční architekturu užitečné, patří role [Uživatel služby DevTest Labs][rbac-devtest] a [Přispěvatel sítě][rbac-network]. 

Uživatele je možné přiřadit k více rolím a jde taky vytvořit vlastní role a udělená oprávnění dál odstupňovat.

d> [!NOTE]
> RBAC neomezuje akce, které může uživatel přihlášený k virtuálnímu počítači provést. Tato oprávnění určuje typ účtu v hostovaném operačním systému.   
> 
> 

Pomocí [protokolů auditu][audit-logs] můžete zjistit akce zřizování a další události virtuálního počítače.

Pokud potřebujete šifrovat disk operačního systému a datové disky, zvažte použití služby [Azure Disk Encryption][disk-encryption]. 

## <a name="solution-deployment"></a>Nasazení řešení
Nasazení pro tuto referenční architekturu je dostupné na [GitHubu][github-folder]. Obsahuje virtuální síť, NSG a jeden virtuální počítač. Pokud chcete tuto architekturu nasadit, postupujte takto: 

1. Klikněte pravým tlačítkem na následující tlačítko a vyberte Otevřít odkaz v nové záložce nebo Otevřít odkaz v novém okně.
   [![Nasazení do Azure](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)
2. Jakmile se odkaz na webu Azure Portal otevře, musíte zadat hodnoty pro některá nastavení: 
   
   * Název **skupiny prostředků** už je v souboru parametrů definovaný. Proto vyberte **Vytvořit nový** a do textového pole zadejte `ra-single-vm-rg`.
   * V rozevíracím seznamu **Umístění** vyberte příslušnou oblast.
   * Obsah textových polí s **kořenovým URI pro šablony** a **kořenovým URI pro parametry** neupravujte.
.   * Vyberte **linux** v **typ operačního systému** rozevíracího pole.
   * Přečtěte si podmínky a ujednání a potom klikněte na zaškrtávací políčko **Souhlasím s podmínkami a ujednáními uvedenými nahoře**.
   * Klikněte na tlačítko **Koupit**.
3. Počkejte, než se nasazení dokončí.
4. Soubory parametrů zahrnují pevně zakódované uživatelské jméno a heslo správce. Důrazně doporučujeme obě tyto hodnoty okamžitě změnit. Na webu Azure Portal klikněte na virtuální počítač s názvem `ra-single-vm0 `. Potom v části **Podpora a řešení potíží** klikněte na **Resetovat heslo**. V rozevíracím seznamu **Režim** vyberte **Resetovat heslo** a potom vyberte nové **uživatelské jméno** a **heslo**. Po kliknutí na tlačítko **Aktualizovat** se nové uživatelské jméno a heslo nastaví.

## <a name="next-steps"></a>Další postup
Pro zajištění vyšší dostupnosti nasaďte dva nebo víc virtuálních počítačů za nástrojem pro vyrovnávání zatížení. Další informace najdete v tématu [Spuštění několika virtuálních počítačů v Azure][multi-vm].

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]:../articles/virtual-machines/windows/create-availability-set.md
[azure-cli]: /cli/azure/get-started-with-az-cli2
[azure-linux]:../articles/virtual-machines/linux/overview.md
[azure-storage]:../articles/storage/common/storage-introduction.md
[blob-snapshot]:../articles/storage/blobs/storage-blob-snapshots.md
[blob-storage]:../articles/storage/common/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]:../articles/virtual-machines/linux/about-disks-and-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]:../articles/virtual-machines/linux/portal-create-fqdn.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]:../articles/virtual-machines/linux/manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]:../articles/virtual-machines/linux/planned-maintenance.md
[premium-storage]:../articles/virtual-machines/windows/premium-storage.md
[rbac]: ../articles/role-based-access-control/overview.md
[rbac-roles]:../articles/role-based-access-control/built-in-roles.md
[rbac-devtest]:../articles/role-based-access-control/built-in-roles.md#devtest-labs-user
[rbac-network]:../articles/role-based-access-control/built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[select-vm-image]:../articles/virtual-machines/linux/cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssh-linux]:../articles/virtual-machines/linux/mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-account-limits]: ../articles/azure-subscription-service-limits.md#storage-limits
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]:../articles/virtual-machines/linux/sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]:../articles/virtual-machines/linux/change-vm-size.md
[vm-size-tables]:../articles/virtual-machines/windows/sizes.md#size-tables
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[components]: #Solution-components
[blocks]: https://github.com/mspnp/template-building-blocks
[0]: ./media/guidance-blueprints/compute-single-vm.png "Architektura samostatného linuxového virtuálního počítače v Azure"

