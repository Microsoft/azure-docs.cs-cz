# <a name="regions-and-availability-for-virtual-machines-in-azure"></a>Oblasti a dostupnost pro virtuální počítače v Azure
Azure funguje v několika datových centrech po celém světě. Tato datová centra jsou seskupená do v geografických oblastí. To vám poskytuje flexibilitu při výběru místa pro sestavení vaší aplikace. Je důležité pochopit, jak a kde se virtuální počítače (VM) v Azure provozují, a také vědět, jaké máte možnosti pro zajištění maximálního výkonu, dostupnosti a redundance. Tento článek poskytuje přehled funkcí redundance a dostupnosti Azure.

## <a name="what-are-azure-regions"></a>Co jsou oblasti Azure?
V definovaných zeměpisné oblasti jako "Západní USA", "Severní Evropa" nebo "jihovýchodní Asie, vytvoříte prostředků Azure. [Seznam oblastí a jejich umístění](https://azure.microsoft.com/regions/) si můžete prohlédnout. V každé oblasti existuje několik datových center, která zajišťují redundanci a dostupnost. Tento přístup poskytuje flexibilitu při navrhování aplikace vytvořit virtuální počítače nejbližší uživatelům a splňují všechny právní, dodržování předpisů nebo daň.

## <a name="special-azure-regions"></a>Speciální oblasti Azure
Azure má některé speciální oblasti, které chcete používat při sestavení se vaše aplikace pro dodržování předpisů nebo právních důvodů. Mezi tyto speciální oblasti patří:

* **USA (Gov) – Iowa** a **USA (Gov) – Virginia**.
  * Fyzická a logická síťově izolovaná instance Azure pro partnery a úřady státní správy USA, která je obsluhovaná prověřenými občany USA. Zahrnuje další certifikace dodržování předpisů, jako je [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) a [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Další informace o službě [Azure Government](https://azure.microsoft.com/features/gov/)
* **Severní Čína** a **Východní Čína**
  * Tyto oblasti jsou dostupné prostřednictvím jedinečného partnerství mezi společnostmi Microsoft a 21Vianet, kdy společnost Microsoft nespravuje tato datová centra přímo. Další informace najdete v tématu [Microsoft Azure v Číně](http://www.windowsazure.cn/).
* **Střední Německo** a **Severovýchodní Německo**
  * Tyto oblasti jsou k dispozici prostřednictvím datový model zplnomocněnec které zákaznická data zůstane v Německu pod kontrolou systémů T německých Telekom společnosti, který funguje jako zplnomocněnec němčině data.

## <a name="region-pairs"></a>Párování oblastí
Každá oblast Azure je spárovaná s jinou oblastí na stejném území (jako je USA, Evropa nebo Asie). Tento přístup umožňuje replikaci prostředků (jako je třeba úložiště virtuálních počítačů) napříč daným územím, která by měla snížit pravděpodobnost, že přírodní katastrofy, občanské nepokoje, výpadky napájení nebo fyzické výpadky sítě ovlivní obě oblasti současně. Mezi další výhody párování oblastí patří:

* V případě rozsáhlejšího výpadku Azure je z každého páru jedna oblast určená jako prioritní. To pomáhá zkrátit čas potřebný k obnovení aplikací. 
* Plánované aktualizace Azure se pro spárované oblasti nasazují po jedné. Tím se minimalizují prostoje a riziko výpadku aplikací.
* Pro daňové účely a účely vymáhání zákonů jsou data nadále uložená na stejném území jako spárovaná oblast (a výjimkou oblasti Brazílie – jih).

Mezi příklady párů oblasti patří:

| Primární | Sekundární |
|:--- |:--- |
| Západní USA |Východ USA |
| Severní Evropa |Západní Evropa |
| Jihovýchodní Asie |Východní Asie |

Úplný [seznam spárovaných oblastí najdete tady](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="feature-availability"></a>Dostupnost funkcí
Některé služby nebo funkce virtuálních počítačů jsou dostupné jenom v některých oblastech, třeba konkrétní velikosti virtuálních počítačů nebo typy úložišť. Existují také globální služby Azure, které nevyžadují výběr konkrétní oblasti, jako je [Azure Active Directory](../articles/active-directory/active-directory-whatis.md), [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md) nebo [Azure DNS](../articles/dns/dns-overview.md). Možnost zkontrolovat [dostupnost služeb Azure v jednotlivých oblastech](https://azure.microsoft.com/regions/#services) vám pomůže při navrhování prostředí pro vaše aplikace. Můžete také [prostřednictvím kódu programu dotazu podporované velikosti virtuálních počítačů a omezení v každé oblasti](../articles/azure-resource-manager/resource-manager-sku-not-available-errors.md).

## <a name="storage-availability"></a>Dostupnost úložiště
Znalost území a oblastí Azure je důležitá, pokud zvažujete dostupné možnosti replikace úložiště. Možnosti replikace závisejí na typu úložiště.

**Azure Managed Disks**
* Místně redundantní úložiště (LRS)
  * Replikuje data třikrát v rámci oblasti, ve které jste vytvořili účet úložiště.

**Disky založené na účtu úložiště**
* Místně redundantní úložiště (LRS)
  * Replikuje data třikrát v rámci oblasti, ve které jste vytvořili účet úložiště.
* Zónově redundantní úložiště (ZRS)
  * Replikuje data třikrát v rámci dvou nebo tří zařízení, a to v jedné oblasti nebo napříč dvěma oblastmi.
* Geograficky redundantní úložiště (GRS)
  * Replikuje data do sekundární oblasti, která je od primární oblasti vzdálená stovky kilometrů.
* Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)
  * Replikuje data do sekundární oblasti (stejně jako u GRS), ale k datům v sekundárním umístění poskytuje přístup jenom pro čtení.

V následující tabulce najdete stručný přehled rozdílů jednotlivých typů replikace:

| Strategie replikace | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Data se replikují napříč různými zařízeními. |Ne |Ano |Ano |Ano |
| Data je možné načíst ze sekundárního i primárního umístění. |Ne |Ne |Ne |Ano |
| Počet kopií dat uchovávaných na samostatných uzlech |3 |3 |6 |6 |

Další informace o [možnostech replikace služby Azure Storage najdete tady](../articles/storage/common/storage-redundancy.md). Další informace o spravovaných discích najdete v tématu [Přehled služby Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

### <a name="storage-costs"></a>Cena za uložení
Ceny se liší v závislosti na zvolené dostupnosti a typu úložiště.

**Azure Managed Disks**
* Disky spravované Premium jsou zajišťované Solid-State disků (SSD) a standardní disky spravované jsou zajišťované disky regulární roztočený. Služba Managed Disks úrovně Premium i Standard se účtuje na základě zřízené kapacity disku.

**Nespravované disky**
* Storage úrovně Premium je zálohovaný Solid-State disků (SSD) a je účtován podle kapacity disku.
* Storage úrovně Standard se zálohuje pomocí běžných rotujících disků a účtuje se na základě využité kapacity a požadované dostupnosti úložiště.
  * Za přenos dat geografické replikace se u RA-GRS účtují navíc náklady na šířku pásma pro replikaci těchto dat do jiné oblasti Azure.

Informace o cenách pro různé typy úložišť a možnosti dostupnosti najdete v tématu [Ceny za Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="availability-sets"></a>Skupiny dostupnosti
Skupina dostupnosti je logické seskupení virtuálních počítačů v datovém centru, které umožňují na platformě Azure, abyste pochopili, jak vaše aplikace sestavena k poskytování redundance a dostupnosti. Doporučujeme, že dvě nebo více virtuálních počítačů jsou vytvořeny v rámci sadu k poskytování vysoce dostupné aplikace a podle dostupnosti [99,95 % smlouva Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Neexistuje žádné náklady pro sadu dostupnosti sám sebe, platíte jenom pro každou instanci virtuálního počítače, který vytvoříte. Pokud [Azure Premium Storage](../articles/virtual-machines/windows/premium-storage.md) využívá jeden virtuální počítač, Azure SLA se vztahuje na neplánovanou údržbu. 

Skupina dostupnosti se skládá ze dvou další seskupení, které ochranu proti selhání hardwaru a povolit aktualizace bezpečně použít-poruch domén (FDs) a aktualizace domény (UDs). O správě dostupnosti [virtuálních počítačů s Linuxem](../articles/virtual-machines/linux/manage-availability.md) nebo [Windows](../articles/virtual-machines/windows/manage-availability.md) si můžete přečíst víc.

### <a name="fault-domains"></a>Domény selhání
Doména selhání je logická skupina hardwarových komponent, které sdílejí společný zdroj napájení a síťový přepínač (je obdobou stojanu v místním datovém centru). Když vytváříte virtuální počítače v rámci skupiny dostupnosti, platforma Azure je automaticky distribuuje do těchto domén selhání. Tento přístup omezuje dopady potenciálního selhání fyzického hardwaru, výpadků sítě nebo přerušení napájení.

### <a name="update-domains"></a>Aktualizační domény
Aktualizační doména je logická skupina hardwarových komponent, u kterých je možné provést údržbu nebo restart současně. Když vytváříte virtuální počítače v rámci skupiny dostupnosti, platforma Azure je automaticky distribuuje do těchto aktualizačních domén. Tento přístup zajišťuje, že při pravidelné údržbě Azure zůstává vždycky nejméně jedna instance vaší aplikace spuštěná. Restartování aktualizačních domén během plánované údržby nemusí probíhat sekvenčně, ale vždycky se restartuje jenom jedna aktualizační doména.

### <a name="managed-disk-fault-domains"></a>Spravované domény selhání disku
Virtuální počítače, které používají [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md), odpovídají doménám selhání spravovaných disků (pokud se použije spravovaná skupina dostupnosti). Toto uspořádání zajišťuje, aby všechny spravované disky připojené k virtuálnímu počítači byly umístěné ve stejné doméně selhání spravovaných disků. Ve spravované skupině dostupnosti je možné vytvořit jenom virtuální počítače se spravovanými disky. Počet domén selhání spravovaných disků se liší podle oblasti – buď dvě, nebo tři domény selhání na oblast. Další informace o těchto spravovaných domén selhání disku pro [virtuální počítače s Linuxem](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) nebo [virtuálních počítačů Windows](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

## <a name="availability-zones"></a>Dostupnost zóny

[Dostupnost zóny](../articles/availability-zones/az-overview.md) (preview), nastaví alternativu k dostupnosti, rozbalte položku úroveň řízení, budete muset zachovat dostupnost aplikace a data na virtuální počítače. Zóna dostupnosti je fyzicky oddělená zóna v oblasti Azure. Existují tři zóny dostupnosti za podporovanou oblast Azure. Každou zónu dostupnosti má samostatnou spotřeby zdroje, sítě a chlazení a logicky oddělené od dalších dostupnost zóny v rámci oblasti Azure. Pomocí architektury řešení pro použití replikované virtuální počítače v zóny, můžete chránit vaše aplikace a data před ztrátou datacentru. Pokud dojde k narušení jednu zónu, pak replikované aplikace a data jsou okamžitě dostupné v jiné zóně. 

![Dostupnost zóny](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

[!INCLUDE [availability-zones-preview-statement.md](availability-zones-preview-statement.md)]

Další informace o nasazení [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) nebo [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) virtuálního počítače v zóně dostupnosti.

## <a name="next-steps"></a>Další postup
Teď můžete tyto funkce pro zajištění redundance a dostupnosti začít používat a vytvořit prostředí Azure. Informace o doporučených postupech najdete v tématu věnovaném [osvědčeným postupům pro zajištění dostupnosti v Azure](../articles/best-practices-availability-checklist.md).

