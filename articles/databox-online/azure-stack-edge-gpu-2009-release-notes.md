---
title: Poznámky k verzi Azure Stack Edge pro GA | Microsoft Docs
description: Popisuje kritické otevřené problémy a řešení pro Azure Stack verzi pro, na kterých běží Obecná dostupnost.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: c43f7ba52ed0f6018ee32583011bb92786708119
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91460471"
---
# <a name="azure-stack-edge-pro-with-gpu-general-availability-ga-release-notes"></a>Zpráva k vydání verze pro obecné dostupnosti GPU (GA) Azure Stack Edge pro

Následující poznámky k verzi identifikují kritické otevřené problémy a vyřešené problémy pro vydání obecné dostupnosti (GA) pro vaše zařízení Azure Stack Edge pro v zařízeních s grafickým procesorem.

Poznámky k verzi se průběžně aktualizují a při zjištění kritických problémů vyžadujících alternativní řešení se přidají. Před nasazením zařízení Azure Stack Edge pro pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Tento článek se týká verze **Azure Stack Edge pro 2009** , která se mapuje na číslo verze softwaru **2.1.1364.2110**.

## <a name="whats-new"></a>Co je nového

Ve verzi Azure Stack Edge 2009 jsou k dispozici následující nové funkce. 

- **Třídy úložiště** – v této verzi jsou dostupné třídy úložiště, které vám umožní dynamicky zřizovat úložiště. Další informace najdete v tématu [Správa úložiště Kubernetes na zařízení GPU pro Azure Stack Edge pro](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning). 
- **Řídicí panel Kubernetes se serverem metrik** – v této verzi se přidá řídicí panel Kubernetes s doplňkem serveru metrik. Řídicí panel můžete použít k získání přehledu o aplikacích, které běží na vašem zařízení Azure Stack Edge pro, zobrazení stavu prostředků clusteru Kubernetes a zobrazení všech chyb, ke kterým došlo v zařízení. Server metrik agreguje využití CPU a paměti napříč Kubernetes prostředky na zařízení. Další informace najdete v tématu [Použití řídicího panelu Kubernetes k monitorování zařízení GPU Azure Stack Edge pro](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- **Azure ARC s povoleným Kubernetes na Azure Stack Edge pro** – zahájení této verze, můžete nasadit úlohy aplikace na zařízení Azure Stack Edge pro prostřednictvím Kubernetes s povoleným ARC Azure. Azure ARC je hybridní Nástroj pro správu, který umožňuje nasazení aplikací do clusterů Kubernetes. Další informace najdete v tématu [nasazení úloh prostřednictvím ARC Azure na zařízení Azure Stack Edge pro](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).  

## <a name="known-issues"></a>Známé problémy 

Následující tabulka poskytuje souhrn známých problémů pro zařízení Azure Stack Edge pro.

| Ne. | Příznak | Problém | Alternativní řešení/komentáře |
| --- | --- | --- | --- |
|**1.**|Funkce ve verzi Preview |Pro tuto verzi GA jsou k dispozici tyto funkce: místní Azure Resource Manager, virtuální počítače, Kubernetes, Kubernetes ARC Azure, služba multi-Process (MPS) pro grafický procesor (MP) pro zařízení s Azure Stack Edge pro.  |Tyto funkce budou všeobecně dostupné v pozdější verzi. |
| **2.** |Azure Stack Edge pro + Azure SQL | Vytvoření databáze SQL vyžaduje přístup správce.   |Proveďte následující kroky místo kroků 1-2 v [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database) . <ul><li>V místním uživatelském rozhraní zařízení povolte výpočetní rozhraní. Vyberte **compute > port # > povolit pro výpočetní > použít.**</li><li>Stáhnout `sqlcmd` na klientském počítači z https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Připojte se k IP adrese rozhraní COMPUTE (port, který byl povolený), a přidejte na konec adresy znak ", 1401".</li><li>Poslední příkaz bude vypadat takto: Sqlcmd-S {Interface IP}, 1401-U SA-P "Strong! Passw0rd".</li>Pak by se měly shodovat kroky 3-4 z aktuální dokumentace. </li></ul> |
| **3.** |Aktualizovat| Přírůstkové změny objektů BLOB obnovených prostřednictvím **aktualizace** se nepodporují. |U koncových bodů objektů BLOB jsou částečné aktualizace objektů BLOB po obnovení možné kvůli tomu, že se aktualizace nebudou nahrávat do cloudu. Například posloupnost akcí, jako například:<ul><li>Vytvoření objektu BLOB v cloudu Nebo odstraňte dříve nahraný objekt BLOB ze zařízení.</li><li>Obnovte objekt BLOB z cloudu do zařízení pomocí funkce aktualizovat.</li><li>Aktualizujte jenom část objektu BLOB pomocí rozhraní REST API sady Azure SDK.</li></ul>Tyto akce můžou vést k tomu, že aktualizované oddíly objektu BLOB se v cloudu neaktualizují. <br>**Alternativní řešení**: Nahraďte celé objekty BLOB pomocí nástrojů, jako je například Robocopy nebo běžné kopírování souborů prostřednictvím Průzkumníka nebo příkazového řádku.|
|**4.**|Throttling|Pokud se během omezování nepovolují nové zápisy do zařízení, zápisy provedené klientem systému souborů NFS selžou s chybou oprávnění odepřeno.| Tato chyba se zobrazí níže:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: Nejde vytvořit adresář ' test ': oprávnění bylo odepřeno.|
|**čl.**|Přijímání Blob Storage|Pokud používáte AzCopy verze 10 pro ingestování úložiště BLOB, spusťte AzCopy s následujícím argumentem: `Azcopy <other arguments> --cap-mbps 2000`| Pokud tato omezení nejsou k dispozici pro AzCopy, může potenciálně Odeslat velký počet požadavků na zařízení a způsobit problémy se službou.|
|**6.**|Účty vrstveného úložiště|Při používání vrstvených účtů úložiště platí následující:<ul><li> Jsou podporovány pouze objekty blob bloku. Objekty blob stránky nejsou podporovány.</li><li>Neexistuje žádná podpora snímků ani kopírování API.</li><li> Ingestování úloh Hadoop přes `distcp` není podporované, protože používá operaci kopírování silným způsobem.</li></ul>||
|**čl.**|Připojení ke sdílené složce NFS|Pokud kopírujete více procesů do stejné sdílené složky a `nolock` atribut se nepoužívá, mohou se během kopírování zobrazit chyby.|`nolock`Atribut musí být předán příkazu Mount ke zkopírování souborů do sdílené složky systému souborů NFS. Například: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**8.**|Cluster Kubernetes|Při použití aktualizace na zařízení, na kterém běží cluster Kubernetes, se virtuální počítače s Kubernetes restartují a restartují. V této instanci se po aktualizaci automaticky obnoví jenom lusky nasazené se zadanými replikami.  |Pokud jste vytvořili jednotlivé lusky mimo řadič replikace bez určení sady replik, pak se tyto lusky po aktualizaci zařízení automaticky neobnoví. Tyto lusky bude nutné obnovit.<br>Sada replik nahrazuje lusky, které jsou z nějakého důvodu odstraněny nebo ukončeny, jako například selhání uzlu nebo upgrade výpadku uzlu. Z tohoto důvodu doporučujeme použít sadu replik i v případě, že vaše aplikace vyžaduje jenom jedno pod.|
|**9.**|Cluster Kubernetes|Kubernetes v Azure Stack Edge pro je podporován pouze s Helm V3 nebo novějším. Další informace najdete v [nejčastějších dotazech: odebrání do pokladny](https://v3.helm.sh/docs/faq/).|
|**10pruhový.**|Kubernetes s podporou Azure Arc |Pro vydání GA se Kubernetes Azure ARC s povoleným aktualizací aktualizuje z verze 0.1.18 na 0.2.9. Vzhledem k Azure Stack hraničnímu zařízení s povoleným Kubernetesm hraniční aktualizace není podpora Azure ARC povolena, bude nutné znovu nasadit Kubernetes s povoleným ARC Azure.|Postupujte takto:<ol><li>[Použijte software zařízení a aktualizace Kubernetes](azure-stack-edge-gpu-install-update.md).</li><li>Připojte se k [rozhraní PowerShell zařízení](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Odeberte stávajícího agenta Azure ARC. Zadejte: `Remove-HcsKubernetesAzureArcAgent` .</li><li>Nasaďte [Azure ARC do nového prostředku](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Nepoužívejte existující prostředek Azure ARC.</li></ol>|
|**odst.**|Kubernetes s podporou Azure Arc|Nasazení ARC Azure se nepodporuje, pokud je webový proxy server nakonfigurovaný na zařízení Azure Stack Edge pro.||
|**12,5.**|Kubernetes |Port 31000 je vyhrazený pro řídicí panel Kubernetes. Podobně platí, že ve výchozí konfiguraci jsou IP adresy 172.28.0.1 a 172.28.0.10 rezervované pro služby Kubernetes a základní službu DNS v uvedeném pořadí.|Nepoužívejte rezervované IP adresy.|
|**13,5.**|Kubernetes |Kubernetes v současné době nepovoluje služby pro vyrovnávání zatížení ve více protokolech. Například služba DNS, která by musela naslouchat na TCP i UDP. |Pokud chcete toto omezení Kubernetes vyřešit pomocí MetalLB, můžete vytvořit dvě služby (jednu pro TCP, jednu pro protokol UDP) na stejném pod selektor. Tyto služby používají stejný klíč pro sdílení a specifikace. loadBalancerIP ke sdílení stejné IP adresy. Pokud máte více služeb než dostupné IP adresy, můžete sdílené IP adresy také sdílet. <br> Další informace najdete v tématu [sdílení IP adres](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**čtrnáct.**|Cluster Kubernetes|Stávající moduly Azure IoT Edge Marketplace můžou vyžadovat úpravy ke spuštění IoT Edge na Azure Stack hraničním zařízení.|Další informace najdete v tématu Změna Azure IoT Edgech modulů z webu Marketplace na používání Azure Stack hraničního zařízení.<!-- insert link-->|
|**15.**|Kubernetes |Připojení vázaných na soubor nejsou u Azure IoT Edge v Kubernetes v zařízení Azure Stack Edge podporována.|IoT Edge používá vrstvu překladu k překladu `ContainerCreate` možností do Kubernetes konstrukcí. Vytváření `Binds` map v adresáři hostpath, což znamená, že připojení vázaných na soubor nemůže být svázána s cestami v kontejnerech IoT Edge. Pokud je to možné, namapujte nadřazený adresář.|
|**16bitovém.**|Kubernetes |Pokud přenesete vlastní certifikáty pro IoT Edge a přidáte je na vaše zařízení Azure Stack Edge po tom, co je na zařízení nakonfigurované výpočetní výkon, nebudou se nové certifikáty vyzvednout.|Pokud chcete tento problém obejít, měli byste před konfigurací výpočtů na zařízení nahrávat certifikáty. Pokud je výpočetní prostředí už nakonfigurované, [Připojte se k rozhraní PowerShell zařízení a spusťte příkazy IoT Edge](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Restart `iotedged` a `edgehub` lusky.|
|**sedmnáct.**|Certifikáty |V některých případech může aktualizace stavu certifikátu v místním uživatelském rozhraní trvat několik sekund. |Může to mít vliv na následující scénáře v místním uživatelském rozhraní.<ul><li>Sloupec **stav** na stránce **certifikáty** .</li><li>Dlaždice **zabezpečení** na **stránce Začínáme.**</li><li>Dlaždice **Konfigurace** na stránce **Přehled** .</li></ul>  |
|**sedmnáct.**|IoT Edge |Moduly nasazené prostřednictvím IoT Edge nemůžou používat síť hostitele. | |
|**let.**|COMPUTE + Kubernetes |COMPUTE/Kubernetes nepodporuje webový proxy server NTLM. ||
|**čl.**|COMPUTE + webový proxy server + aktualizace |Pokud máte výpočty nakonfigurované s webovým proxy serverem, může aktualizace COMPUTE selhat. |Doporučujeme, abyste před aktualizací zakázali výpočetní prostředky. |

<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->




## <a name="next-steps"></a>Další kroky

- [Příprava na nasazení Azure Stack Edge pro zařízení s grafickým procesorem](azure-stack-edge-gpu-deploy-prep.md)

