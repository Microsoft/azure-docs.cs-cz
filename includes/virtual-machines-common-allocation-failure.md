
Pokud není Azure problém řešený v tomto článku, navštivte [fóra Azure na webu MSDN a Stack Overflow](https://azure.microsoft.com/support/forums/). Problém můžete účtovat na tyto fóra nebo na @AzureSupport na Twitteru. Navíc můžete soubor žádost o podporu Azure tak, že vyberete **získat podporu** na [podporu Azure](https://azure.microsoft.com/support/options/) lokality.

## <a name="general-troubleshooting-steps"></a>Obecné řešení potíží
### <a name="troubleshoot-common-allocation-failures-in-the-classic-deployment-model"></a>Řešení potíží s běžné chyby v přidělení v modelu nasazení classic
Tyto kroky můžete vyřešit velký počet chyb alokace ve virtuálních počítačích:

* Změňte velikost virtuálního počítače na jinou velikost virtuálního počítače.<br>
    Klikněte na tlačítko **procházet všechny** > **virtuálních počítačů (klasické)** > virtuálního počítače > **nastavení** > **velikost**. Podrobné pokyny najdete v tématu [změnit velikost virtuálního počítače](https://msdn.microsoft.com/library/dn168976.aspx).
* Odstranit všechny virtuální počítače z cloudové služby a znovu vytvořit virtuální počítače.<br>
    Klikněte na tlačítko **procházet všechny** > **virtuálních počítačů (klasické)** > virtuálního počítače > **odstranit**. Potom klikněte na **vytvořit prostředek** > **výpočetní** > [bitovou kopii virtuálního počítače].

### <a name="troubleshoot-common-allocation-failures-in-the-azure-resource-manager-deployment-model"></a>Řešení potíží s běžné chyby v přidělení v modelu nasazení Azure Resource Manager
Tyto kroky můžete vyřešit velký počet chyb alokace ve virtuálních počítačích:

* Zastavit (zrušit přidělení) nastavte všechny virtuální počítače ve stejné dostupnosti a pak restartujte každé z nich.<br>
    Zastavit: klikněte na tlačítko **skupiny prostředků** > vaší skupiny prostředků > **prostředky** > vaše skupina dostupnosti > **virtuální počítače** > virtuálního počítače >  **Zastavit**.
  
    Po všech virtuálních počítačů zastavit, vyberte první virtuální počítač a klikněte na **spustit**.

## <a name="background-information"></a>Základní informace
### <a name="how-allocation-works"></a>Jak funguje přidělení
Servery v datových centrech Azure jsou rozdělené do clusterů. Za normálních okolností se požadavek na přidělení zkouší v několika clusterech, ale je možné, že určitá omezení požadavku na přidělení donutí platformu Azure, aby tento požadavek vyzkoušela jenom v jednom clusteru. V tomto článku budeme označovat to jako "připnuli do clusteru s podporou." Obrázek 1 níže znázorňuje malá normální přidělení, které dojde k pokusu o několik clusterů. Obrázek 2 znázorňuje malá přidělení, má připnuli k clusteru 2, protože se jedná, je hostitelem existující sady CS_1 cloudové služby nebo dostupnost.
![Diagram přidělení](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Proč nastat chyby v přidělení
Při požadavku na přidělení je ukotvena do clusteru, existuje vyšší pravděpodobnost nedaří najít volné prostředky, protože fond zdrojů k dispozici je menší. Kromě toho pokud požadavek na přidělení umístěn v clusteru, ale tento cluster nepodporuje typ prostředku, který jste požadovali, vaši žádost se nezdaří i v případě, že cluster má uvolnění prostředků. Obrázek 3 níže znázorňuje tento případ, kdy připojené přidělení nezdaří, protože cluster jenom candidate nemá uvolnění prostředků. Obrázek 4 znázorňuje tento případ, kde připojené přidělení nezdaří, protože pouze candidate clusteru nepodporuje požadovaná velikost virtuálního počítače, i když cluster má uvolnění prostředků.

![Došlo k chybě definovaného přidělení](./media/virtual-machines-common-allocation-failure/Allocation2.png)

## <a name="detailed-troubleshoot-steps-specific-allocation-failure-scenarios-in-the-classic-deployment-model"></a>Podrobné řešení potíží s kroky konkrétní přidělení selhání scénáře v modelu nasazení classic
Tady uvádíme běžné scénáře přidělení, které způsobí požadavku na přidělení být připojena. Můžeme vám podrobně každý scénář dále v tomto článku.

* Změnit velikost virtuálního počítače nebo přidat virtuální počítače nebo instance rolí do existující cloudové služby
* Restartování částečně zastavena (deallocated) virtuálních počítačů
* Restartujte plně zastaveném (deallocated) virtuálních počítačů
* Pracovní nebo produkční nasazení (platforma jako služba pouze)
* Skupina vztahů (blízkosti virtuálního počítače nebo služby)
* Na základě vztahů skupiny virtuální sítě

Jakmile se zobrazí chyba přidělení, najdete v části Pokud některé popsané scénáře vztahují na vaši chyby. Chyba přidělení vrácený platformy Azure slouží k identifikaci odpovídající scénář. Pokud je připnutý váš požadavek, odeberte některé z Připnutí omezení otevřít žádost o více clusterů, a tím zvýšit riziko přidělení úspěch.

Obecně platí tak dlouho, dokud chyba neindikuje "požadovaná velikost virtuálního počítače není podporováno", můžete vždy opakovat později, protože dostatek prostředků, může být uvolněna v clusteru, aby dokázala pojmout vaši žádost. Pokud problém je, že požadovaná velikost virtuálního počítače nepodporuje, zkuste jinou velikost virtuálního počítače. Jedinou možností, jinak je odebrání Připnutí omezení.

Dvě běžné scénáře selhání souvisí s skupiny vztahů. V minulosti skupiny vztahů byl použit k poskytování těsné blízkosti instancí virtuálních počítačů nebo služeb nebo byl použit a umožňují tak vytváření virtuální sítě. Se zavedením regionálních virtuálních sítí skupiny vztahů již nejsou potřeba pro vytvoření virtuální sítě. S snížení latence sítě v infrastrukturu Azure se změnila doporučení pro virtuální počítač nebo služba blízkosti používat skupiny vztahů.

Diagram 5 níže obsahuje řadu scénářů (připojené) přidělení.
![Taxonomii připojené přidělení](./media/virtual-machines-common-allocation-failure/Allocation3.png)

> [!NOTE]
> Chyby uvedené v každém scénáři přidělení je zkrácené formě. Odkazovat [vyhledávací řetězec chyby](#Error string lookup) pro podrobné informace o chybě řetězce.
> 
> 

## <a name="allocation-scenario-resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Přidělení scénář: Změna velikosti virtuálního počítače nebo přidat virtuální počítače nebo instance rolí do existující cloudové služby
**Chyba**

Upgrade_VMSizeNotSupported nebo GeneralError

**Příčinu Připnutí clusteru**

Požadavek na změnit velikost virtuálního počítače nebo virtuálního počítače nebo role instance přidat do existující cloudové služby musí být pokus v původní cluster, který hostuje stávající cloudovou službu. Vytváření nové cloudové služby umožňuje platformy Azure do jiného clusteru, která má volných prostředků nebo podporuje velikost virtuálního počítače, který jste požadovali, najít.

**Alternativní řešení**

Pokud je chyba Upgrade_VMSizeNotSupported *, zkuste jinou velikost virtuálního počítače. Pokud pomocí jinou velikost virtuálního počítače není možné, ale pokud se dá použít jinou virtuální adresu IP (VIP), vytvořte novou cloudovou službu pro hostování nový virtuální počítač a přidat novou cloudovou službu do regionální virtuální síť, kde běží existujících virtuálních počítačů. Pokud vaše stávající cloudovou službu nepoužívá regionální virtuální síť, můžete přesto vytvořit novou virtuální síť pro novou cloudovou službu a potom se připojte vaše [existující virtuální sítě do nové virtuální sítě](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Další informace [regionálních virtuálních sítí](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Pokud je chyba GeneralError *, je pravděpodobné, že typ prostředku (například konkrétní velikost virtuálního počítače) se podporuje v clusteru, ale clusteru nemá volné prostředky v tuto chvíli. Podobá se výše uvedené scénáře, přidejte požadované výpočetních prostředků procesem vytvoření nové cloudové služby (Všimněte si, že má novou cloudovou službu používat jiný virtuální IP adresy) a připojení ke cloudovým službám pomocí regionální virtuální síť.

## <a name="allocation-scenario-restart-partially-stopped-deallocated-vms"></a>Scénář přidělení: restartování částečně zastavena (deallocated) virtuálních počítačů
**Chyba**

GeneralError *

**Příčinu Připnutí clusteru**

Částečné navrácení znamená (deallocated) jeden nebo více, ale ne všechny virtuální počítače v cloudové službě byla zastavena. Při zastavení (zrušit přidělení) a virtuální počítač, jsou vydávány přidružené prostředky. Restartování tohoto zastaveném (deallocated) virtuálního počítače je proto novou žádost o přidělení. Restartování virtuálních počítačů v částečně deallocated Cloudová služba je ekvivalentní při přidávání virtuálních počítačů do stávající cloudovou službu. Požadavek na přidělení musí být pokus v původní cluster, který hostuje stávající cloudovou službu. Vytváření jiné cloudové služby umožňuje platformy Azure do jiného clusteru, který má volný prostředek nebo podporuje velikost virtuálního počítače, který jste požadovali, najít.

**Alternativní řešení**

Pokud se dá použít jiný virtuální IP adresy, odstranit zastaveném (deallocated) virtuálních počítačů (ale zachovat přidružené disků) a přidejte virtuální počítače zpět pomocí jiné cloudové služby. Pro připojení vašich cloudových službách použijte regionální virtuální síť:

* Pokud vaše stávající cloudovou službu používá regionální virtuální síť, stačí přidáte novou cloudovou službu do stejné virtuální síti.
* Pokud vaše stávající cloudovou službu nepoužívá regionální virtuální síť, vytvořit novou virtuální síť pro novou cloudovou službu a potom [připojit existující virtuální síť k nové virtuální sítě](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Další informace [regionálních virtuálních sítí](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="allocation-scenario-restart-fully-stopped-deallocated-vms"></a>Scénář přidělení: restartování plně zastavena (deallocated) virtuálních počítačů
**Chyba**

GeneralError *

**Příčinu Připnutí clusteru**

Úplné navrácení znamená, že jste zastavili (nepřiřazeném) všech virtuálních počítačů z cloudové služby. Přidělení žádosti, které chcete restartovat tyto virtuální počítače musí být pokus v původním clusteru, který je hostitelem cloudové služby. Vytváření nové cloudové služby umožňuje platformy Azure do jiného clusteru, která má volných prostředků nebo podporuje velikost virtuálního počítače, který jste požadovali, najít.

**Alternativní řešení**

Pokud se dá použít jiný virtuální IP adresy, odstranit původní zastaveném (deallocated) virtuálních počítačů (ale zachovat přidružené disky) a odstranit odpovídající cloudové služby (přidružené výpočetní prostředky uvolnila již při jeho zastavení (deallocated) virtuálních počítačů). Vytvořte novou cloudovou službu, chcete-li přidat virtuální počítače zpět.

## <a name="allocation-scenario-stagingproduction-deployments-platform-as-a-service-only"></a>Scénář přidělení: pracovní nebo produkční nasazení (platforma jako služba pouze)
**Chyba**

New_General * nebo New_VMSizeNotSupported *

**Příčinu Připnutí clusteru**

Nasazení pracovní a provozní nasazení cloudové služby jsou hostované ve stejném clusteru. Když přidáte druhé nasazení, požadavek na odpovídající přidělení se pokusí ve stejném clusteru, který je hostitelem prvním nasazení.

**Alternativní řešení**

Odstraňte prvním nasazení a původní Cloudová služba a znovu nasaďte cloudové služby. Tato akce může být umístěn prvním nasazení v clusteru, který má dostatek volných prostředků, aby vyhovovaly obou nasazení nebo v clusteru, který podporuje velikosti virtuálních počítačů, které jste požádali.

## <a name="allocation-scenario-affinity-group-vmservice-proximity"></a>Scénář přidělení: Skupina vztahů (blízkosti virtuálního počítače nebo služby)
**Chyba**

New_General * nebo New_VMSizeNotSupported *

**Příčinu Připnutí clusteru**

Všechny výpočetní zdroj přiřazený do skupiny vztahů je vázaný na jednom clusteru. Nový prostředek výpočetní požadavků v tom, že jsou skupiny vztahů pokus ve stejném clusteru, kde jsou hostované existující prostředky. To platí, jestli jsou vytvořeny nové prostředky prostřednictvím novou cloudovou službu nebo stávající cloudovou službu.

**Alternativní řešení**

Pokud skupiny vztahů je nezbytné, není pomocí skupiny vztahů nebo seskupení výpočetní prostředky do více skupin vztahů.

## <a name="allocation-scenario-affinity-group-based-virtual-network"></a>Scénář přidělení: na základě vztahů skupiny virtuální sítě
**Chyba**

New_General * nebo New_VMSizeNotSupported *

**Příčinu Připnutí clusteru**

Předtím, než byly zavedeny regionálních virtuálních sítí, bylo potřeba virtuální síť přidružit skupiny vztahů. V důsledku toho výpočetní prostředky, které jsou umístěny do skupiny vztahů je vázána ve stejné omezení, jak je popsáno v "přidělení scénář: Skupina vztahů (virtuálního počítače nebo služby blízkosti)" část výše. Výpočetní prostředky jsou svázané s jeden cluster.

**Alternativní řešení**

Pokud není nutné skupinu vztahů, vytvořte nový regionální virtuální síť pro nové prostředky, které přidáváte, a potom [připojit existující virtuální síť k nové virtuální sítě](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Další informace [regionálních virtuálních sítí](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Alternativně můžete [migrace na základě vztahů skupiny virtuální sítě na regionální virtuální síť](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)a poté znovu přidejte požadované prostředky.

## <a name="detailed-troubleshooting-steps-specific-allocation-failure-scenarios-in-the-azure-resource-manager-deployment-model"></a>Podrobné řešení potíží s kroky konkrétní přidělení selhání scénáře v modelu nasazení Azure Resource Manager
Tady uvádíme běžné scénáře přidělení, které způsobí požadavku na přidělení být připojena. Můžeme vám podrobně každý scénář dále v tomto článku.

* Změnit velikost virtuálního počítače nebo přidat virtuální počítače nebo instance rolí do existující cloudové služby
* Restartování částečně zastavena (deallocated) virtuálních počítačů
* Restartujte plně zastaveném (deallocated) virtuálních počítačů

Jakmile se zobrazí chyba přidělení, najdete v části Pokud některé popsané scénáře vztahují na vaši chyby. Chyba přidělení vrácený platformy Azure slouží k identifikaci odpovídající scénář. Pokud vaše žádost je připojena k existujícímu clusteru, odeberte některé z Připnutí omezení otevřít žádost o více clusterů, a tím zvýšit riziko přidělení úspěch.

Obecně platí tak dlouho, dokud chyba neindikuje "požadovaná velikost virtuálního počítače není podporováno", můžete vždy opakovat později, protože dostatek prostředků, může být uvolněna v clusteru, aby dokázala pojmout vaši žádost. Pokud problém je, že požadovaná velikost virtuálního počítače není podporovaná, najdete dále alternativní řešení.

## <a name="allocation-scenario-resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Přidělení scénář: Změna velikosti virtuálního počítače nebo virtuální počítače přidat do stávající sadu dostupnosti
**Chyba**

Upgrade_VMSizeNotSupported * nebo GeneralError *

**Příčinu Připnutí clusteru**

Požadavek na změnit velikost virtuálního počítače nebo virtuálního počítače přidat do stávající sadu dostupnosti musí být pokus v původní cluster, který hostuje stávající sadu dostupnosti. Vytvoření nové sady dostupnosti. umožňuje platformy Azure do jiného clusteru, která má volných prostředků nebo podporuje velikost virtuálního počítače, který jste požadovali, najít.

**Alternativní řešení**

Pokud je chyba Upgrade_VMSizeNotSupported *, zkuste jinou velikost virtuálního počítače. Pokud pomocí jinou velikost virtuálního počítače není možné, zastavte všechny virtuální počítače v sadě dostupnosti. Můžete změnit velikost virtuálního počítače, který bude přidělení virtuálního počítače do clusteru, který podporuje požadovaná velikost virtuálního počítače.

Pokud je chyba GeneralError *, je pravděpodobné, že typ prostředku (například konkrétní velikost virtuálního počítače) se podporuje v clusteru, ale clusteru nemá volné prostředky v tuto chvíli. Pokud virtuální počítač může být součástí jiné skupině dostupnosti, vytvořte nový virtuální počítač ve skupině (ve stejné oblasti) různé dostupnosti. Tento nový virtuální počítač můžete pak přidá do stejné virtuální síti.  

## <a name="allocation-scenario-restart-partially-stopped-deallocated-vms"></a>Scénář přidělení: restartování částečně zastavena (deallocated) virtuálních počítačů
**Chyba**

GeneralError *

**Příčinu Připnutí clusteru**

Částečné navrácení znamená, že jste zastavili (deallocated) jeden nebo více, ale ne všechny, virtuální počítače ve skupině dostupnosti nastavena. Při zastavení (zrušit přidělení) a virtuální počítač, jsou vydávány přidružené prostředky. Restartování tohoto zastaveném (deallocated) virtuálního počítače je proto novou žádost o přidělení. Restartování virtuálních počítačů v sadě dostupnosti částečně deallocated je ekvivalentní při přidávání virtuálních počítačů do stávající sadu dostupnosti. Požadavek na přidělení musí být pokus v původní cluster, který hostuje stávající sadu dostupnosti.

**Alternativní řešení**

Zastavte všechny virtuální počítače ve skupině před restartováním první dostupnosti. Tím bude zajištěno, že se nový pokus o přidělení spustit a, do nového clusteru lze vybrat s dostupné kapacity.

## <a name="allocation-scenario-restart-fully-stopped-deallocated"></a>Scénář přidělení: restartování plně zastavena (deallocated)
**Chyba**

GeneralError *

**Příčinu Připnutí clusteru**

Úplné navrácení znamená, že jste zastavili (nepřiřazeném) všech virtuálních počítačích v nastavení dostupnosti. Požadavek na přidělení, znovu tyto virtuální počítače se zaměří na všechny clustery, které podporují požadované velikosti.

**Alternativní řešení**

Vyberte novou velikost virtuálního počítače přidělit. Pokud to nefunguje, zkuste to prosím znovu později.

<a name="Error string lookup"></a>

## <a name="error-string-lookup"></a>Vyhledávací řetězec chyby
**New_VMSizeNotSupported***

"Velikosti virtuálních počítačů (či kombinace velikostí virtuálních počítačů) požadovaná tímto nasazením nejde zřídit kvůli omezení žádost o nasazení. Pokud je to možné zkuste uvolnit omezení, jako jsou třeba vazby virtuální sítě, nasazení v hostované službě není žádné další nasazení a v jiné skupině vztahů nebo bez skupin vztahů, nebo vyzkoušejte nasazení v jiné oblasti."

**New_General***

"Přidělení selhalo; nelze vyhovět omezení v požadavku. Požadované nasazení nové služby je vázán do skupiny vztahů, nebo jeho cílem virtuální síť nebo je v rámci této hostované služby v existujícím nasazení. Některé z těchto podmínek omezuje nové nasazení do konkrétních prostředků Azure. Opakujte akci později nebo zkuste zmenšit velikost virtuálního počítače nebo počet instancí role. Případně pokud je to možné, odeberte výše uvedené omezení nebo vyzkoušejte nasazení v jiné oblasti."

**Upgrade_VMSizeNotSupported***

"Nejde upgradovat nasazení. Požadovaná velikost virtuálního počítače XXX nemusí být k dispozici v prostředích, která podporují existující nasazení. Prosím opakujte akci později, zkuste to znovu s jinou velikost virtuálního počítače nebo zmenšete počet instancí role nebo vytvořit nasazení pod prázdnou hostovanou službou s novou skupinou vztahů nebo bez vazby na skupinu vztahů."

**GeneralError***

"Serveru došlo k vnitřní chybě. Opakujte žádost." Nebo "Nepodařilo se vytvořit přidělení pro službu."

