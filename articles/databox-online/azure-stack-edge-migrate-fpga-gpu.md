---
title: Průvodce migrací pro Azure Stack Edge pro FPGA na fyzické zařízení GPU
description: Tato příručka obsahuje pokyny k migraci úloh ze zařízení Azure Stack Edge pro FPGA do zařízení GPU Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: ce063e27620c3b0ccec9336e7a45ebb2ee1ad10d
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101687"
---
# <a name="migrate-workloads-from-an-azure-stack-edge-pro-fpga-to-an-azure-stack-edge-pro-gpu"></a>Migrace úloh z Azure Stack Edge pro FPGA do GPU pro Azure Stack Edge pro

Tento článek popisuje, jak migrovat úlohy a data ze zařízení Azure Stack Edge pro FPGA do zařízení GPU Azure Stack Edge pro. Postup migrace zahrnuje Přehled migrace, včetně porovnání mezi těmito dvěma zařízeními, požadavky na migraci, podrobný postup a ověřování následovaný čištěním.

<!--Azure Stack Edge Pro FPGA devices will reach end-of-life in February 2024. If you are considering new deployments, we recommend that you explore Azure Stack Edge Pro GPU devices for your workloads.-->

## <a name="about-migration"></a>Informace o migraci

Migrace je proces přesunutí úloh a dat aplikací z jednoho umístění úložiště do jiného. To zahrnuje vytvoření přesné kopie aktuálních dat organizace z jednoho úložného zařízení do jiného úložného zařízení – to je vhodné, pokud chcete rušit nebo zakázat aktivní aplikace a pak přesměrovat všechny aktivity vstupu a výstupu (v/v) na nové zařízení. 

Tato příručka k migraci poskytuje podrobný návod k postupům, které jsou potřeba k migraci dat ze zařízení Azure Stack Edge pro FPGA do zařízení GPU Azure Stack Edge pro. Tento dokument je určený pro odborníky v oblasti informačních technologií (IT) a pro pracovníky v oblasti znalostí, kteří zodpovídají za provoz, nasazení a správu Azure Stack hraničních zařízení v datacentru. 

V tomto článku se na zařízení Azure Stack Edge pro FPGA říká *zdrojové* zařízení a zařízení GPU Azure Stack Edge pro je *cílové* zařízení. 

## <a name="comparison-summary"></a>Souhrn porovnání

Tato část poskytuje srovnávací Souhrn funkcí mezi grafickým procesorem Azure Stack Edge pro a zařízeními Azure Stack Edge pro FPGA. Hardware ve zdrojovém i cílovém zařízení je v podstatě identický a liší se jenom s ohledem na kartu hardwarovou akceleraci a kapacitu úložiště. 

|    Schopnost  | Grafický procesor Azure Stack Edge pro (cílové zařízení)  | Azure Stack Edge pro FPGA (zdrojové zařízení)|
|----------------|-----------------------|------------------------|
| Hardware       | Hardwarová akcelerace: 1 nebo 2 grafické procesory NVIDIA T4 <br> Výpočetní výkon, paměť, síťové rozhraní, jednotka napájení, specifikace napájecího kabelu jsou stejné jako u zařízení s FPGA.  | Hardwarová akcelerace: Intel Arria 10 FPGA <br> Výpočetní výkon, paměť, síťové rozhraní, jednotka napájení, specifikace napájecího kabelu jsou stejné jako zařízení s grafickým procesorem.          |
| Použitelné úložiště | 4,19 TB <br> Po rezervování prostoru pro odolnost proti chybám a interní použití | 12,5 TB <br> Po rezervování prostoru pro interní použití |
| Zabezpečení       | Certifikáty |                                                     |
| Úlohy      | IoT Edge úlohy <br> Úlohy virtuálních počítačů <br> Úlohy Kubernetes| IoT Edge úlohy |
| Ceny        | [Ceny](https://azure.microsoft.com/pricing/details/azure-stack/edge/) | [Ceny](https://azure.microsoft.com/pricing/details/azure-stack/edge/)|

## <a name="migration-plan"></a>Plán migrace

Při vytváření plánu migrace Vezměte v úvahu následující informace:

- Vytvořte plán migrace. 
- Při migraci dat může docházet k výpadkům. Doporučujeme, abyste naplánovali migraci během výpadku časového období údržby, protože proces je rušivý. Konfigurace se nastaví a obnoví v tomto výpadku, jak je popsáno dále v tomto dokumentu.
- Seznamte se s celkovým počtem výpadků a sdělují ho všem zúčastněným stranám.
- Identifikujte místní data, která je potřeba migrovat ze zdrojového zařízení. V rámci preventivních opatření se ujistěte, že všechna data ve stávajícím úložišti mají poslední zálohu. 


## <a name="migration-considerations"></a>Posouzení migrace 

Než budete pokračovat v migraci, vezměte v úvahu následující informace: 

- Zařízení GPU Azure Stack Edge pro se nedá aktivovat proti prostředku Azure Stack Edge pro FPGA. Pro zařízení GPU Azure Stack Edge pro se musí vytvořit nový prostředek, jak je popsáno v tématu [Vytvoření pořadí GPU pro Azure Stack Edge pro](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).
- Machine Learning modely nasazené na zdrojovém zařízení, které používaly FPGA, bude nutné změnit pro cílové zařízení pomocí GPU. Chcete-li získat nápovědu k modelům, můžete kontaktovat podpora Microsoftu. Vlastní modely nasazené na zdrojovém zařízení, které nepoužívaly FPGA (jenom použité CPU), by měly fungovat tak, jak jsou na cílovém zařízení (pomocí procesoru).
- Moduly IoT Edge nasazené na zdrojovém zařízení mohou vyžadovat změny, aby je bylo možné úspěšně nasadit na cílovém zařízení. 
- Zdrojové zařízení podporuje protokoly NFS 3,0 a 4,1. Cílové zařízení podporuje jenom protokol NFS 3,0.
- Zdrojové zařízení podporuje protokoly SMB a NFS. Cílové zařízení podporuje kromě protokolů SMB a NFS pro sdílené složky úložiště prostřednictvím protokolu REST pomocí účtů úložiště.
- Přístup ke sdíleným složkám na zdrojovém zařízení je prostřednictvím IP adresy, zatímco přístup ke sdílení na cílovém zařízení je přes název zařízení.

## <a name="migration-steps-at-a-glance"></a>Postup migrace na první pohled

Tato tabulka shrnuje Celkový tok migrace a popisuje kroky potřebné k migraci a umístění, kde provést tento postup.

| V této fázi | Provést tento krok| V tomto zařízení |
|---------------|-------------|----------------|
| Příprava zdrojového zařízení *       | 1. záznam konfiguračních dat <br>2. zálohování sdílených dat <br>3. Příprava úloh IoT Edge| Zdrojové zařízení  |
| Příprava cílového zařízení *       |1. vytvořit novou objednávku <br>2. konfigurace a aktivace| Cílové zařízení  |
| Migrace dat       | 1. migrace dat ze sdílených složek <br>2. znovu nasaďte IoT Edge úlohy.| Cílové zařízení  |
| Ověření dat            |Ověřit migrovaná data |Cílové zařízení  |
| Vyčištění, návrat              |Vymazání dat a návrat| Zdrojové zařízení  |

**Zdrojová a cílová zařízení je možné připravit paralelně.*

## <a name="prepare-source-device"></a>Příprava zdrojového zařízení

Příprava zahrnuje, že identifikujete sdílené cloudy, místní sdílené složky Edge a moduly IoT Edge nasazené na zařízení. 

### <a name="1-record-configuration-data"></a>1. záznam konfiguračních dat

Proveďte tyto kroky na zdrojovém zařízení prostřednictvím místního uživatelského rozhraní.

Poznamenejte si konfigurační data na *zdrojovém* zařízení. Pomocí [kontrolního seznamu nasazení](azure-stack-edge-gpu-deploy-checklist.md) vám pomůžou zaznamenat konfiguraci zařízení. Během migrace použijete tyto konfigurační informace ke konfiguraci nového cílového zařízení. 

### <a name="2-back-up-share-data"></a>2. zálohování sdílených dat

Data zařízení mohou být jednoho z následujících typů:

- Data ve sdílených cloudech Edge
- Data v místních sdílených složkách

#### <a name="data-in-edge-cloud-shares"></a>Data ve sdílených cloudech Edge

Hraniční Cloud sdílí data vrstev z vašeho zařízení do Azure. Tyto kroky proveďte na *zdrojovém* zařízení prostřednictvím Azure Portal. 

- Vytvořte seznam všech sdílených složek Edge a uživatelů, které máte na zdrojovém zařízení.
- Vytvořte seznam všech plánů šířky pásma, které máte. Na cílovém zařízení znovu vytvoříte tyto plány šířky pásma.
- V závislosti na dostupné šířce pásma sítě nakonfigurujte na svém zařízení plány šířky pásma, aby bylo možné maximalizovat data vrstvená do cloudu. Tím se minimalizují místní data na zařízení.
- Ujistěte se, že jsou sdílené složky plně vrstveny v cloudu. To můžete potvrdit kontrolou stavu sdílení v Azure Portal.  

#### <a name="data-in-edge-local-shares"></a>Data v místních sdílených složkách Edge

Data v hraničních místních sdílených složkách zůstanou v zařízení. Tyto kroky proveďte na *zdrojovém* zařízení prostřednictvím Azure Portal. 

- Vytvořte seznam místních sdílených složek Edge, které máte na zařízení.
- Vzhledem k tomu, že je to jednorázová migrace dat, vytvořte kopii místní sdílené složky Edge na jiný místní server. Ke zkopírování dat můžete použít nástroje pro kopírování, jako je například `robocopy` (SMB) nebo `rsync` (NFS). Volitelně možná už máte nasazené řešení ochrany dat od třetí strany, abyste mohli zálohovat data v místních sdílených složkách. Pro použití se zařízeními Azure Stack Edge pro FPGA se podporují Tato řešení třetích stran:

    | Software jiných výrobců           | Odkaz na řešení                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Podrobnosti získáte od Cohesity.          |
    | CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Podrobnosti získáte od CommVault.          |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Podrobnosti získáte od společnosti Veritas.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Podrobnosti získáte od Veeam. |


### <a name="3-prepare-iot-edge-workloads"></a>3. Příprava úloh IoT Edge

- Pokud jste nasadili IoT Edge moduly a používáte akceleraci FPGA, možná budete muset moduly před tím, než je spustíte, na zařízení GPU upravit. Postupujte podle pokynů v části [úprava IoT Edgech modulů](azure-stack-edge-gpu-modify-fpga-modules-gpu.md). 

<!--- If you have deployed IoT Edge workloads, the configuration data is shared on a share on the device. Back up the data in these shares.-->


## <a name="prepare-target-device"></a>Příprava cílového zařízení

### <a name="1-create-new-order"></a>1. vytvořit novou objednávku

Pro *cílové* zařízení musíte vytvořit novou objednávku (a nový prostředek). Cílové zařízení musí být aktivováno proti zdroji GPU a nikoli k FPGA prostředku.

Chcete-li umístit objednávku, [vytvořte nový Azure Stack hraničního prostředku](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource) v Azure Portal.


### <a name="2-set-up-activate"></a>2. nastavení, aktivace

Je nutné nastavit a aktivovat *cílové* zařízení s novým prostředkem, který jste vytvořili dříve. 

Pomocí těchto kroků nakonfigurujete *cílové* zařízení prostřednictvím Azure Portal:

1. Shromážděte informace požadované v [kontrolním seznamu nasazení](azure-stack-edge-gpu-deploy-checklist.md). Můžete použít informace, které jste uložili z konfigurace zdrojového zařízení. 
1. [Rozbalením](azure-stack-edge-gpu-deploy-install.md#unpack-the-device), [připojení k racku](azure-stack-edge-gpu-deploy-install.md#rack-the-device) a [zapojení kabelů do zařízení](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Připojte se k místnímu uživatelskému rozhraní zařízení](azure-stack-edge-gpu-deploy-connect.md).
1. Nakonfigurujte síť pomocí jiné sady IP adres (Pokud používáte statické IP adresy) než ty, které jste použili pro původní zařízení. Podívejte se, jak [nakonfigurovat nastavení sítě](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Přiřaďte stejný název zařízení jako staré zařízení a zadejte doménu DNS. Viz jak [nakonfigurovat nastavení zařízení](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Nakonfigurujte certifikáty na novém zařízení. Přečtěte si téma [Konfigurace certifikátů](azure-stack-edge-gpu-deploy-configure-certificates.md).
1. Získá aktivační klíč z Azure Portal a aktivuje nové zařízení. Podívejte se, jak [zařízení aktivovat](azure-stack-edge-gpu-deploy-activate.md).

Teď můžete obnovit sdílená data a nasadit úlohy, které jste spustili na starém zařízení.

## <a name="migrate-data"></a>Migrace dat

Teď budete kopírovat data ze zdrojového zařízení do sdílených složek na hraničních cloudech a na vaše *cílové* zařízení na místní sdílené složky Edge.

### <a name="1-from-edge-cloud-shares"></a>1. ze sdílených cloudů Edge

Pomocí těchto kroků synchronizujte data na hraničních sdílených cloudech na cílovém zařízení:

1. [Přidejte sdílené složky](azure-stack-edge-j-series-manage-shares.md#add-a-share) odpovídající názvům sdílených složek vytvořeným na zdrojovém zařízení. Ujistěte se, že při vytváření sdílených složek je **Výběr kontejneru objektů BLOB** nastavený na **použití existující** možnosti a pak vyberte kontejner, který jste použili s předchozím zařízením.
1. [Přidejte uživatele](azure-stack-edge-j-series-manage-users.md#add-a-user) , kteří mají přístup k předchozímu zařízení.
1. [Aktualizujte sdílená](azure-stack-edge-j-series-manage-shares.md#refresh-shares) data z Azure. Tím se vyžádá všechna cloudová data z existujícího kontejneru do sdílených složek.
1. Vytvořte znovu plány šířky pásma, které se mají přidružit ke sdíleným složkám. Podrobné pokyny najdete v tématu [Přidání plánu šířky pásma](azure-stack-edge-j-series-manage-bandwidth-schedules.md#add-a-schedule) .


### <a name="2-from-edge-local-shares"></a>2. z hraničních místních sdílení

Možná jste nasadili řešení zálohování třetí strany, které zajistí ochranu místních sdílených dat pro úlohy IoT. Teď budete muset tato data obnovit.

Po úplné konfiguraci náhradního zařízení povolte zařízení pro místní úložiště. 

Pomocí těchto kroků obnovte data z místních sdílených složek:

1. [Proveďte konfiguraci výpočtů na zařízení](azure-stack-edge-gpu-deploy-configure-compute.md).
1. Přidejte do cílového zařízení všechny místní sdílené složky. Přečtěte si podrobný postup v tématu [Přidání místní sdílené složky](azure-stack-edge-j-series-manage-shares.md#add-a-local-share).
1. Přístup ke sdíleným složkám SMB na zdrojovém zařízení bude používat IP adresy, zatímco na cílovém zařízení použijete název zařízení. Viz [připojení ke sdílené složce SMB na grafickém procesoru Azure Stack Edge pro](azure-stack-edge-j-series-deploy-add-shares.md#connect-to-an-smb-share). Pokud se chcete připojit ke sdíleným složkám systému souborů NFS na cílovém zařízení, budete muset použít nové IP adresy přidružené k zařízení. Viz [připojení ke sdílené složce NFS na grafickém procesoru Azure Stack Edge pro](azure-stack-edge-j-series-deploy-add-shares.md#connect-to-an-nfs-share). 

    Pokud jste data sdílených složek zkopírovali do zprostředkujícího serveru přes protokol SMB/NFS, můžete tato data zkopírovat do sdílených složek na cílovém zařízení. Data můžete také zkopírovat přímo ze zdrojového zařízení, pokud je zdrojové i cílové zařízení *online*.

    Pokud jste použili software jiného výrobce k zálohování dat v místních sdílených složkách, budete muset spustit postup obnovení poskytovaný řešením ochrany dat podle vlastního výběru. Viz odkazy v následující tabulce.

    | Software jiných výrobců           | Odkaz na řešení                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Podrobnosti získáte od Cohesity.          |
    | CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Podrobnosti získáte od CommVault. |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Podrobnosti získáte od společnosti Veritas.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Podrobnosti získáte od Veeam. |

### <a name="3-redeploy-iot-edge-workloads"></a>3. znovu nasaďte IoT Edge úlohy.

Po přípravě modulů IoT Edge budete muset na cílovém zařízení nasadit IoT Edge úlohy. Pokud máte v nasazení IoT Edgech modulů nějaké chyby, přečtěte si téma:

- [Běžné problémy a jejich řešení pro Azure IoT Edge](../iot-edge/troubleshoot-common-errors.md)a 
- [IoT Edge chyby modulu runtime] [Správa zařízení GPU Azure Stack Edge pro pomocí Windows PowerShellu](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-iot-edge-errors)

## <a name="verify-data"></a>Ověření dat

Po migraci ověřte, že se migrují všechna data a že na cílovém zařízení byly nasazené úlohy.

## <a name="erase-data-return"></a>Vymazání dat, vrácení

Po dokončení migrace dat smažte místní data a vraťte zdrojové zařízení. Postupujte podle kroků v části [vrácení zařízení Azure Stack Edge pro](azure-stack-edge-return-device.md).


## <a name="next-steps"></a>Další kroky

[Přečtěte si, jak nasadit IoT Edge úlohy na zařízení GPU pro Azure Stack Edge pro.](azure-stack-edge-gpu-deploy-compute-module-simple.md)
