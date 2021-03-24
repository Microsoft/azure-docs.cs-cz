---
title: Zpráva k vydání verze Azure Stack Edge pro 2103
description: Popisuje kritické otevřené problémy a řešení pro Azure Stack vydání verze pro s 2103.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/23/2021
ms.author: alkohli
ms.openlocfilehash: 846d4a259f0fcd204bcad6c898efc999c3765fd3
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962560"
---
# <a name="azure-stack-edge-2103-release-notes"></a>Zpráva k vydání verze Azure Stack Edge 2103

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Následující poznámky k verzi identifikují kritické otevřené problémy a vyřešené problémy pro vydání 2103 pro Azure Stack hraničních zařízení. Tyto poznámky k verzi platí pro PROCESORy Azure Stack Edge pro, Azure Stack Edge pro R a Azure Stack hraničních Mini R. Funkce a problémy, které odpovídají konkrétnímu modelu, se označují všude, kde je to možné.

Poznámky k verzi se průběžně aktualizují a při zjištění kritických problémů vyžadujících alternativní řešení se přidají. Před nasazením zařízení pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Tento článek se týká verze **Azure Stack Edge 2103** , která se mapuje na číslo verze softwaru **2.2.1540.2890**. Tento software se dá použít pro vaše zařízení, pokud používáte aspoň Azure Stack software Edge 2010 (2.1.1377.2170).

## <a name="whats-new"></a>Novinky

Ve verzi Azure Stack Edge 2103 jsou k dispozici následující nové funkce. 
 
- **Nové funkce Virtual Machines** – od této verze můžete na virtuálních počítačích provádět následující operace správy prostřednictvím Azure Portal:
    - Přidat nebo odebrat více síťových rozhraní pro existující virtuální počítač.
    - Přidání nebo odebrání více disků pro existující virtuální počítač.
    - Změňte velikost virtuálního počítače.
    - Přidejte vlastní data při nasazení virtuálního počítače s Windows nebo Linuxem.

  Můžete se také [připojit k konzole virtuálních počítačů na svém zařízení](azure-stack-edge-gpu-connect-virtual-machine-console.md) a řešit potíže s virtuálním počítačem.

- **Připojte se k rozhraní PowerShell přes HTTPS** – Tato verze se už nebudete moct na zařízení přes *protokol HTTP* otevřít pomocí vzdálené relace PowerShellu. Ve výchozím nastavení se pro všechny relace použije *https* . Další informace najdete v tématu Jak se [připojit k rozhraní PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) vašeho zařízení.

- **Vylepšení výpočetní služby** – provedli jsme několik vylepšení a vylepšení, včetně těchto:

    - **Celková kvalita výpočetní platformy**. Tato verze obsahuje opravy chyb pro zlepšení celkové kvality výpočetní platformy. Přečtěte si [problémy opravené ve verzi 2103](#issues-fixed-in-2103-release). 
    - **Komponenty výpočetní platformy**. Pro bitovou kopii virtuálního počítače se nastavily aktualizace zabezpečení. IoT Edge a Azure ARC pro verze Kubernetes se taky aktualizovaly.
    - **Diagnostika**. Nové rozhraní API se uvolní pro kontrolu stavu prostředku a sítě. Můžete se připojit k rozhraní PowerShell zařízení a pomocí `Test-HcsKubernetesStatus` příkazu ověřit připravenost sítě zařízení.
    - **Shromažďování protokolů** , které by vedlo k lepšímu ladění. 
    - **Infrastruktura s výstrahami** , která vám umožní detekovat konflikty IP adres pro IP adresy Compute. 
    - **Kombinace úloh** Kubernetes a místních Azure Resource Manager. 

- **Proaktivní protokolování – při** spuštění této verze je ve výchozím nastavení na vašem zařízení zapnuté proaktivní shromažďování protokolů. Díky této funkci mohou společnosti Microsoft shromažďovat protokoly proaktivním způsobem na základě indikátorů stavu systému, které umožňují efektivně řešit problémy se zařízením. Další informace najdete v tématu [proaktivní shromažďování protokolů na vašem zařízení](azure-stack-edge-gpu-proactive-log-collection.md).

## <a name="issues-fixed-in-2103-release"></a>Problémy opravené ve verzi 2103

V následující tabulce jsou uvedeny problémy, které byly vydány v předchozích verzích a opraveny v aktuální verzi.

| Ne. | Funkce | Problém | 
| --- | --- | --- |
|**1.**|Kubernetes |Pokud je povolen webový proxy server, nefunguje registr kontejneru Edge.|
|**2.**|Kubernetes |Registr kontejneru Edge nefunguje s moduly IoT Edge.| 

## <a name="known-issues-in-2103-release"></a>Známé problémy v 2103 Release

Následující tabulka poskytuje souhrn známých problémů verze 2103.

| Ne. | Funkce | Problém | Alternativní řešení/komentáře |
| --- | --- | --- | --- |
|**1.**|Funkce ve verzi Preview |V této verzi jsou k dispozici tyto funkce: místní Azure Resource Manager, virtuální počítače, Správa cloudu pro virtuální počítače, Správa cloudu Azure Kubernetes, síť VPN pro Azure Stack Edge pro R a Azure Stack hraniční Mini R, služba multi-Process Service (MPS) pro grafický procesor Azure Stack Edge pro a je dostupná ve verzi Preview.  |Tyto funkce budou všeobecně dostupné v pozdějších verzích. |
|**2.**|Virtuální počítače GPU |Před touto verzí se v průběhu aktualizace nespravoval životní cyklus virtuálních počítačů GPU. Proto při aktualizaci na verzi 2103 se virtuální počítače GPU během aktualizace automaticky nezastaví. Před aktualizací zařízení budete muset virtuální počítače GPU `stop-stayProvisioned` před tím, než budete aktualizovat, ručně zastavit pomocí příznaku. Další informace najdete v tématu [pozastavení nebo vypnutí virtuálního počítače](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm).<br> Všechny virtuální počítače GPU spuštěné před aktualizací se spustí po aktualizaci. V těchto případech se úlohy běžící na virtuálních počítačích řádně neukončí. A virtuální počítače můžou po aktualizaci potenciálně skončit v nežádoucím stavu. <br>Všechny virtuální počítače GPU, které se zastavily prostřednictvím nástroje `stop-stayProvisioned` před aktualizací, se po aktualizaci automaticky spustí. <br>Pokud zastavíte virtuální počítače GPU prostřednictvím Azure Portal, budete muset po aktualizaci zařízení ručně spustit virtuální počítač.| Pokud používáte virtuální počítače GPU s Kubernetes, před aktualizací zastavte virtuální počítače GPU hned. <br>Po zastavení virtuálních počítačů GPU převezme Kubernetes jednotky GPU, které používaly virtuální počítače. <br>Čím déle jsou virtuální počítače GPU v zastaveném stavu, tím větší je riziko, že Kubernetes převezme procesory GPU. |
|**3.**|Rozšíření virtuálního počítače vlastního skriptu |Na virtuálních počítačích s Windows, které se vytvořily v dřívější verzi, došlo k známému problému a zařízení se aktualizovalo na 2103. <br> Pokud na tyto virtuální počítače přidáte rozšíření vlastních skriptů, Agent hosta virtuálního počítače s Windows (jenom verze 2.7.41491.901) se zablokuje v aktualizaci, což způsobí vypršení časového limitu nasazení rozšíření. | Náhradní řešení tohoto problému: <ul><li> Připojte se k virtuálnímu počítači s Windows pomocí protokolu RDP (Remote Desktop Protocol). </li><li> Ujistěte se, že `waappagent.exe` je spuštěný na počítači: `Get-Process WaAppAgent` . </li><li> Pokud není `waappagent.exe` spuštěný, restartujte `rdagent` službu: `Get-Service RdAgent` \| `Restart-Service` . Počkejte 5 minut.</li><li> Když `waappagent.exe` je spuštěný, ukončete `WindowsAzureGuest.exe` proces. </li><li>Po ukončení procesu se proces spustí znovu s novější verzí.</li><li>Ověřte, zda je verze agenta hosta virtuálního počítače s Windows 2.7.41491.971 pomocí tohoto příkazu: `Get-Process WindowsAzureGuestAgent` \| `fl ProductVersion` .</li><li>[Nastavte rozšíření vlastních skriptů na virtuálním počítači s Windows](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md). </li><ul> | 
|**4.**|Služba multi-Process (MPS) |Po aktualizaci softwaru zařízení a clusteru Kubernetes se nastavení MPS pro úlohy nezachová.   |[Znovu povolte MPS a znovu](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) nasaďte úlohy, které používaly MPS. |


## <a name="known-issues-from-previous-releases"></a>Známé problémy z předchozích verzí

Následující tabulka poskytuje souhrn známých problémů přenesených z předchozích verzí.

| Ne. | Funkce | Problém | Alternativní řešení/komentáře |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge pro + Azure SQL | Vytvoření databáze SQL vyžaduje přístup správce.   |Proveďte následující kroky místo kroků 1-2 v [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) . <ul><li>V místním uživatelském rozhraní zařízení povolte výpočetní rozhraní. Vyberte **compute > port # > povolit pro výpočetní > použít.**</li><li>Stáhnout `sqlcmd` na klientském počítači z https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Připojte se k IP adrese rozhraní COMPUTE (port, který byl povolený), a přidejte na konec adresy znak ", 1401".</li><li>Poslední příkaz bude vypadat takto: Sqlcmd-S {Interface IP}, 1401-U SA-P "Strong! Passw0rd".</li>Pak by se měly shodovat kroky 3-4 z aktuální dokumentace. </li></ul> |
| **2.** |Aktualizovat| Přírůstkové změny objektů BLOB obnovených prostřednictvím **aktualizace** se nepodporují. |U koncových bodů objektů BLOB jsou částečné aktualizace objektů BLOB po obnovení možné kvůli tomu, že se aktualizace nebudou nahrávat do cloudu. Například posloupnost akcí, jako například:<ul><li>Vytvoření objektu BLOB v cloudu Nebo odstraňte dříve nahraný objekt BLOB ze zařízení.</li><li>Obnovte objekt BLOB z cloudu do zařízení pomocí funkce aktualizovat.</li><li>Aktualizujte jenom část objektu BLOB pomocí rozhraní REST API sady Azure SDK.</li></ul>Tyto akce můžou vést k tomu, že aktualizované oddíly objektu BLOB se v cloudu neaktualizují. <br>**Alternativní řešení**: Nahraďte celé objekty BLOB pomocí nástrojů, jako je například Robocopy nebo běžné kopírování souborů prostřednictvím Průzkumníka nebo příkazového řádku.|
|**3.**|Throttling|Pokud se během omezování nepovolí nové zápisy do zařízení, zapíše se u klienta systému souborů NFS chyba s oprávněním odepřeno.| Tato chyba se zobrazí níže:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: Nejde vytvořit adresář ' test ': oprávnění bylo odepřeno.|
|**4.**|Přijímání Blob Storage|Pokud používáte AzCopy verze 10 pro ingestování úložiště BLOB, spusťte AzCopy s následujícím argumentem: `Azcopy <other arguments> --cap-mbps 2000`| Pokud tato omezení nejsou k dispozici pro AzCopy, mohla by potenciálně Odeslat velký počet požadavků na zařízení, což vede k problémům se službou.|
|**čl.**|Účty vrstveného úložiště|Při používání vrstvených účtů úložiště platí následující:<ul><li> Jsou podporovány pouze objekty blob bloku. Objekty blob stránky nejsou podporovány.</li><li>Neexistuje žádná podpora snímků ani kopírování API.</li><li> Ingestování úloh Hadoop přes `distcp` není podporované, protože používá operaci kopírování silným způsobem.</li></ul>||
|**6.**|Připojení ke sdílené složce NFS|Pokud kopírujete více procesů do stejné sdílené složky a atribut se `nolock` nepoužívá, mohou se během kopírování zobrazit chyby.|`nolock`Atribut musí být předán příkazu Mount ke zkopírování souborů do sdílené složky systému souborů NFS. Příklad: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**čl.**|Cluster Kubernetes|Při použití aktualizace na zařízení, na kterém běží cluster Kubernetes, se virtuální počítače s Kubernetes restartují a restartují. V této instanci se po aktualizaci automaticky obnoví jenom lusky nasazené se zadanými replikami.  |Pokud jste vytvořili jednotlivé lusky mimo řadič replikace bez určení sady replik, nebudou se tyto lusky po aktualizaci zařízení automaticky obnovovat. Tyto lusky bude nutné obnovit.<br>Sada replik nahrazuje lusky, které jsou z nějakého důvodu odstraněny nebo ukončeny, jako například selhání uzlu nebo upgrade výpadku uzlu. Z tohoto důvodu doporučujeme použít sadu replik i v případě, že vaše aplikace vyžaduje jenom jedno pod.|
|**8.**|Cluster Kubernetes|Kubernetes v Azure Stack Edge pro je podporován pouze s Helm V3 nebo novějším. Další informace najdete v [nejčastějších dotazech: odebrání do pokladny](https://v3.helm.sh/docs/faq/).|
|**9.**|Kubernetes s podporou Azure Arc |Pro vydání GA se Kubernetes Azure ARC s povoleným aktualizací aktualizuje z verze 0.1.18 na 0.2.9. Vzhledem k Azure Stack hraničnímu zařízení s povoleným Kubernetesm hraniční aktualizace není podpora Azure ARC povolena, bude nutné znovu nasadit Kubernetes s povoleným ARC Azure.|Postupujte takto:<ol><li>[Použijte software zařízení a aktualizace Kubernetes](azure-stack-edge-gpu-install-update.md).</li><li>Připojte se k [rozhraní PowerShell zařízení](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Odeberte stávajícího agenta Azure ARC. Zadejte: `Remove-HcsKubernetesAzureArcAgent` .</li><li>Nasaďte [Azure ARC do nového prostředku](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Nepoužívejte existující prostředek Azure ARC.</li></ol>|
|**10pruhový.**|Kubernetes s podporou Azure Arc|Nasazení ARC Azure se nepodporuje, pokud je webový proxy server nakonfigurovaný na zařízení Azure Stack Edge pro.||
|**odst.**|Kubernetes |Port 31000 je vyhrazený pro řídicí panel Kubernetes. Port 31001 je vyhrazený pro registr kontejneru Edge. Podobně platí, že ve výchozí konfiguraci jsou IP adresy 172.28.0.1 a 172.28.0.10 rezervované pro služby Kubernetes a základní službu DNS v uvedeném pořadí.|Nepoužívejte rezervované IP adresy.|
|**12,5.**|Kubernetes |Kubernetes v současné době nepovoluje služby pro vyrovnávání zatížení ve více protokolech. Například služba DNS, která by musela naslouchat na TCP i UDP. |Pokud chcete toto omezení Kubernetes vyřešit pomocí MetalLB, můžete vytvořit dvě služby (jednu pro TCP, jednu pro protokol UDP) na stejném pod selektor. Tyto služby používají stejný klíč pro sdílení a specifikace. loadBalancerIP ke sdílení stejné IP adresy. Pokud máte více služeb než dostupné IP adresy, můžete sdílené IP adresy také sdílet. <br> Další informace najdete v tématu [sdílení IP adres](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**13,5.**|Cluster Kubernetes|Stávající moduly Azure IoT Edge Marketplace můžou vyžadovat úpravy ke spuštění IoT Edge na Azure Stack hraničním zařízení.|Další informace najdete v tématu Změna Azure IoT Edgech modulů z webu Marketplace na používání Azure Stack hraničního zařízení.<!-- insert link-->|
|**čtrnáct.**|Kubernetes |Připojení vázaných na soubor nejsou podporovaná Azure IoT Edge v Kubernetes v zařízení Azure Stack Edge.|IoT Edge používá vrstvu překladu k překladu `ContainerCreate` možností do Kubernetes konstrukcí. Vytváření `Binds` map do `hostpath` adresáře, tedy připojení vázaných na soubor nemůže být vázáno na cesty v kontejnerech IoT Edge. Pokud je to možné, namapujte nadřazený adresář.|
|**15.**|Kubernetes |Pokud přenesete vlastní certifikáty pro IoT Edge a přidáte je do vašeho zařízení Azure Stack Edge po tom, co je na zařízení nakonfigurované výpočetní výkon, nezjistí se nové certifikáty.|Pokud chcete tento problém obejít, měli byste před konfigurací výpočtů na zařízení nahrávat certifikáty. Pokud je výpočetní prostředí už nakonfigurované, [Připojte se k rozhraní PowerShell zařízení a spusťte příkazy IoT Edge](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Restart `iotedged` a `edgehub` lusky.|
|**16bitovém.**|Certifikáty |V některých případech může aktualizace stavu certifikátu v místním uživatelském rozhraní trvat několik sekund. |Může to mít vliv na následující scénáře v místním uživatelském rozhraní.<ul><li>Sloupec **stav** na stránce **certifikáty** .</li><li>Dlaždice **zabezpečení** na **stránce Začínáme.**</li><li>Dlaždice **Konfigurace** na stránce **Přehled** .</li></ul>  |
|**sedmnáct.**|IoT Edge |Moduly nasazené prostřednictvím IoT Edge nemůžou používat síť hostitele. | |
|**let.**|COMPUTE + Kubernetes |COMPUTE/Kubernetes nepodporuje webový proxy server NTLM. ||
|**čl.**|Kubernetes + aktualizovat |Starší verze softwaru, například verze 2008, mají problém aktualizace konfliktu časování, který způsobí, že se aktualizace nezdaří s ClusterConnectionException. |Použití novějších buildů by mělo pomoci vyhnout se tomuto problému. Pokud se tento problém opakuje, alternativní řešení je pokus o upgrade, který by měl být v činnosti.|
|**20o**|Internet Explorer|Pokud jsou povolené rozšířené funkce zabezpečení, možná nebudete mít přístup k místním webovým stránkám uživatelského rozhraní. | Zakažte rozšířené zabezpečení a restartujte prohlížeč.|
|**20.**|Řídicí panel Kubernetes | Koncový bod *https* pro řídicí panel Kubernetes s certifikátem SSL se nepodporuje. | |
|**22.**|Kubernetes |Kubernetes nepodporuje ":" v názvech proměnných prostředí, které jsou používány aplikacemi .NET. To je také vyžadováno pro fungování modulu Event Grid IoT Edge, aby fungovalo na Azure Stack hraničních zařízeních a dalších aplikacích. Další informace najdete v [dokumentaci ke službě ASP.NET Core](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration&view=aspnetcore-3.1&preserve-view=true#environment-variables).|Nahraďte ":" dvojitým podtržítkem. Další informace najdete v tématu [problém Kubernetes](https://github.com/kubernetes/kubernetes/issues/53201) .|
|**listopadu.** |Cluster Azure ARC + Kubernetes |Ve výchozím nastavení platí, že při `yamls` odstranění prostředku z úložiště Git se odpovídající prostředky neodstraní z clusteru Kubernetes.  |Pokud chcete odstranění prostředků odstranit z úložiště Git, nastavte `--sync-garbage-collection` v OperatorParams ARC. Další informace najdete v tématu [Odstranění konfigurace](../azure-arc/kubernetes/use-gitops-connected-cluster.md#additional-parameters). |
|**24.**|NFS |Aplikace, které používají připojení ke sdílené složce NFS na vašem zařízení k zápisu dat, by měly používat exkluzivní zápis. To zajišťuje, že se zápisy zapisují na disk.| |
|**0,25.**|Konfigurace výpočtů |Konfigurace výpočtů se nezdařila v konfiguracích sítě, kde brány nebo přepínače nebo směrovače reagují na žádosti protokolu ARP (Address Resolution Protocol) pro systémy, které v síti neexistují.| |
|**26.**|COMPUTE a Kubernetes |Pokud je na zařízení Kubernetes nastavený první, vyplní všechny dostupné GPU. Proto není možné vytvářet Azure Resource Manager virtuální počítače pomocí GPU po nastavení Kubernetes. |Pokud má vaše zařízení 2 GPU, můžete vytvořit jeden virtuální počítač, který používá GPU, a pak nakonfigurovat Kubernetes. V takovém případě Kubernetes použije zbývající dostupný grafický procesor (1 GPU). |


<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->


## <a name="next-steps"></a>Další kroky

- [Aktualizace zařízení](azure-stack-edge-gpu-install-update.md)
