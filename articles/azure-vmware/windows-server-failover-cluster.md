---
title: Cluster s podporou převzetí služeb při selhání Windows serveru v Azure VMware Solution síti vSAN s nativními sdílenými disky
description: Nastavte cluster systému Windows Server s podporou převzetí služeb při selhání (WSFC) na řešení Azure VMware a využijte výhod řešení, která vyžadují službu WSFC.
ms.topic: how-to
ms.date: 03/09/2021
ms.openlocfilehash: 8cb2fdd8d519c665840390a41c95121218750a95
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025964"
---
# <a name="windows-server-failover-cluster-on-azure-vmware-solution-vsan-with-native-shared-disks"></a>Cluster s podporou převzetí služeb při selhání Windows serveru v Azure VMware Solution síti vSAN s nativními sdílenými disky

V tomto článku Vás provedeme procesem nastavení clusteru s podporou převzetí služeb při selhání Windows serveru v řešení VMware Azure. Implementace v tomto článku je určena pro účely testování konceptu a pilotního nasazení. K dispozici je doporučujeme použít konfiguraci CIB (cluster-in-the-box), dokud nebudou dostupné zásady umístění.

Cluster systému Windows Server s podporou převzetí služeb při selhání (WSFC), dříve označovaný jako služba Microsoft Service Cluster Service (MSCS), je funkcí operačního systému Windows Server (OS). WSFC je důležité obchodní funkce a pro mnoho aplikací se vyžaduje. Například WSFC je nutné pro následující konfigurace:

- SQL Server je nakonfigurovaný jako:
  - Instance clusteru s podporou převzetí služeb při selhání (FCI) vždy pro vysokou dostupnost na úrovni instance.
  - Skupina dostupnosti Always On (AG) pro vysokou dostupnost na úrovni databáze.
- Souborové služby systému Windows:
  - Obecná sdílená složka běžící na aktivním uzlu clusteru.
  - Scale-Out souborový server (SOFS), který ukládá soubory do sdílených svazků clusteru (CSV).
  - Prostory úložiště s přímým přístupem (S2D); místní disky používané k vytváření fondů úložiště v různých uzlech clusteru.

Cluster služby WSFC můžete hostovat na různých instancích řešení Azure VMware, označovaných jako cluster-on-box (CAB). Cluster služby WSFC můžete také umístit na jeden uzel řešení Azure VMware. Tato konfigurace se označuje jako cluster-in-the-box (CIB). Pro produkční implementaci nedoporučujeme používat řešení CIB. Nedošlo k selhání jednoho uzlu řešení Azure VMware, všechny uzly clusteru služby WSFC budou vypnuté a aplikace by měla zacházet k výpadkům. Řešení Azure VMware vyžaduje minimálně tři uzly v clusteru privátního cloudu.

Je důležité nasadit podporovanou konfiguraci služby WSFC. Budete chtít, aby vaše řešení bylo podporované v vSphere a pomocí řešení Azure VMware. VMware poskytuje podrobný dokument o službě WSFC v vSphere 6,7, [nastavení pro clustering s podporou převzetí služeb při selhání a Clusterové služby Microsoftu](https://docs.vmware.com/en/VMware-vSphere/6.7/vsphere-esxi-vcenter-server-67-setup-mscs.pdf).

Tento článek se zaměřuje na služby WSFC v systému Windows Server 2016 a Windows Server 2019. Starší verze Windows serveru nejsou v [běžné podpoře](https://support.microsoft.com/lifecycle/search?alpha=windows%20server) , takže je tady nepovažujeme.

Musíte nejdřív [vytvořit WSFC](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster). Další informace o WSFC najdete v tématu věnovaném [clusteringu s podporou převzetí služeb při selhání ve Windows serveru](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview). Informace, které poskytujeme v tomto článku, najdete v tématu nasazení služby WSFC v řešení Azure VMware.

## <a name="prerequisites"></a>Požadavky

- Prostředí řešení Azure VMware
- Instalační médium operačního systému Microsoft Windows Server

## <a name="reference-architecture"></a>Referenční architektura

Řešení Azure VMware poskytuje nativní podporu pro virtualizované služby WSFC. Podporuje trvalé rezervace SCSI-3 (SCSI3PR) na úrovni virtuálního disku. Služba WSFC vyžaduje tuto podporu k arbitrate přístupu ke sdílenému disku mezi uzly. Podpora SCSI3PRs umožňuje konfiguraci služby WSFC s prostředkem disku sdíleným mezi virtuálními počítači nativně v úložišti dat síti vSAN.

Následující diagram znázorňuje architekturu virtuálních uzlů služby WSFC v privátním cloudu řešení Azure VMware. Zobrazuje, kde se řešení Azure VMware nachází, včetně virtuálních serverů služby WSFC (červené pole) ve vztahu k širší platformě Azure. Tento diagram znázorňuje typickou architekturu hvězdicové architektury, ale s využitím Azure Virtual WAN je možné použít podobný instalační program. Obě služby Azure nabízejí všechny další hodnoty.

[![Diagram znázorňující architekturu virtuálních uzlů služby WSFC v privátním cloudu řešení Azure VMware.](media/windows-server-failover-cluster/windows-server-failover-architecture.png)](media/windows-server-failover-cluster/windows-server-failover-architecture.png#lightbox)

## <a name="supported-configurations"></a>Podporované konfigurace

V současné době jsou podporovány následující konfigurace:

- Microsoft Windows Server 2012 nebo novější
- Až pět uzlů clusteringu s podporou převzetí služeb při selhání na cluster
- Až čtyři PVSCSI adaptéry na virtuální počítač
- Až 64 disků na PVSCSI adaptér

## <a name="virtual-machine-configuration-requirements"></a>Požadavky na konfiguraci virtuálního počítače

### <a name="wsfc-node-configuration-parameters"></a>Parametry konfigurace uzlu služby WSFC

- Na každý uzel WSFC nainstalujte nejnovější nástroje VMware.
- Kombinování nesdílených a sdílených disků na jednom virtuálním adaptéru SCSI se nepodporuje. Například pokud systémový disk (jednotka C:) je připojen k SCSI0:0, první sdílený disk by byl připojen k SCSI1:0. Uzel virtuálního počítače služby WSFC má stejný maximální počet virtuálních řadičů SCSI jako běžný virtuální počítač až čtyř (4) virtuálních řadičů SCSI.
- Identifikátory SCSI virtuálních disků by měly být konzistentní mezi všemi hostitelskými uzly stejné služby WSFC.

| **Komponenta** | **Požadavky** |
| --- | --- |
| Verze hardwaru virtuálního počítače | 11 nebo vyšší pro podporu živých vMotion |
| Virtuální síťová karta | VMXNET3 paravirtualizovanými síťové rozhraní (NIC); Povolte na virtuálním síťovém adaptéru škálování na straně příjmu (RSS) v hostovaném programu. |
| Memory (Paměť) | Pro uzly v clusteru služby WSFC použijte úplnou paměť vyhrazení virtuálních počítačů. |
| Zvyšte časový limit vstupně-výstupních operací každého uzlu služby WSFC. | Upravte HKEY \_ Local \_ MACHINE\System\CurrentControlSet\Services\Disk\TimeOutValueSet na 60 sekund nebo více. (Pokud cluster znovu vytvoříte, bude tato hodnota nastavena na výchozí hodnotu, takže ji musíte znovu změnit.) |
| Monitorování stavu clusteru Windows | Aby bylo možné minimálně 10 zmeškaných prezenčních signálů, musí být hodnota parametru SameSubnetThreshold monitorování stavu clusteru systému Windows upravena. Toto je [výchozí nastavení ve Windows serveru 2016](https://techcommunity.microsoft.com/t5/failover-clustering/tuning-failover-cluster-network-thresholds/ba-p/371834). Toto doporučení se vztahuje na všechny aplikace, které používají WSFC, včetně sdílených a nesdílených disků. |

### <a name="wsfc-node---boot-disks-configuration-parameters"></a>Parametry konfigurace pro spouštěcí disky v uzlu WSFC


| **Komponenta** | **Požadavky** |
| --- | --- |
| Typ řadiče SCSI | Adaptér LSI Logic SAS |
| Režim disku | Virtuální |
| Sdílení sběrnice SCSI | Žádné |
| Úprava upřesňujících nastavení pro virtuální řadič SCSI hostující spouštěcí zařízení. | Do každého uzlu WSFC přidejte následující Rozšířená nastavení:<br /> scsiX. returnNoConnectDuringAPD = "TRUE"<br />scsiX. returnBusyOnNoConnectStatus = "FALSE"<br />Kde X je číslo ID řadiče sběrnice SCSI spouštěcího zařízení. Ve výchozím nastavení je X nastavená na 0. |

### <a name="wsfc-node---shared-disks-configuration-parameters"></a>Parametry konfigurace sdílených disků uzlu služby WSFC


| **Komponenta** | **Požadavky** |
| --- | --- |
| Typ řadiče SCSI | VMware Paravirtualize (PVSCSI) |
| Režim disku | Nezávislé – trvalé (krok 2 na ilustraci níže). Pomocí tohoto nastavení zajistíte, aby se všechny disky vyloučily ze snímků. Pro virtuální počítače založené na WSFC se snímky nepodporují. |
| Sdílení sběrnice SCSI | Fyzický (krok 1 na obrázku níže) |
| Příznak pro vícenásobný zápis | Nepoužívá se. |
| Formát disku | Silné zřízené. (Eager s nulovou hodnotou (EZT) není vyžadováno s síti vSAN.) |

:::image type="content" source="media/windows-server-failover-cluster/edit-settings-virtual-hardware.png" alt-text="Snímek obrazovky zobrazující stránku Upravit nastavení pro virtuální hardware":::

## <a name="non-supported-scenarios"></a>Nepodporované scénáře

Pro službu WSFC v řešení Azure VMware se nepodporují tyto funkce:

- Úložiště dat NFS
- Prostory úložiště
- Síti vSAN pomocí služby iSCSI
- Síti vSAN roztaženého clusteru
- Vylepšená kompatibilita vMotion (EVC)
- odolnost proti chybám vSphere (FT)
- Snímky
- Live (online) vMotion úložiště
- N – virtualizace ID portu (NPIV)

Horké změny hardwaru virtuálních počítačů můžou rušit prezenční signál mezi uzly služby WSFC.

Následující aktivity nejsou podporované a můžou způsobit převzetí služeb při selhání uzlu služby WSFC:

- Horké přidání paměti
- Přidání procesoru za chodu
- Použití snímků
- Zvětšení velikosti sdíleného disku
- Pozastavení a obnovení stavu virtuálního počítače
- Překročení výše vyhodnoceného množství paměti při prohození virtuálních počítačů nebo v bublinách paměti virtuálního počítače
- Připojit se k místnímu souboru VMDK i v případě, že není přidružený k řadiči SCSI pro sdílení sběrnice SCSI

## <a name="configure-wsfc-with-shared-disks-on-azure-vmware-solution-vsan"></a>Konfigurace služby WSFC se sdílenými disky v Azure VMware Solution síti vSAN

1. Ujistěte se, že je k dispozici prostředí Active Directory.
2. Vytvořte virtuální počítače v úložišti dat síti vSAN.
3. Zapněte na všech virtuálních počítačích, nakonfigurujte název hostitele, IP adresy, připojte všechny virtuální počítače k doméně služby Active Directory a nainstalujte nejnovější dostupné aktualizace operačního systému.
4. Nainstalujte nejnovější nástroje VMware.
5. Povolte a nakonfigurujte funkci Windows Server failover cluster na všech virtuálních počítačích.
6. Konfigurace určujícího clusteru pro kvorum (sdílená složka sdílené složky funguje správně).
7. Vypněte všechny uzly clusteru služby WSFC.
8. Přidejte jeden nebo více řadičů SCSI Paravirtual (až čtyř) do každého virtuálního počítače v rámci služby WSFC. Použijte nastavení pro předchozí odstavce.
9. Na prvním uzlu clusteru přidejte všechny potřebné sdílené disky pomocí **Přidat nový**  >  **pevný disk** zařízení. Sdílení disku by mělo zůstat **neurčené** (výchozí) a režim disku jako **nezávisle – trvalé**. Připojte ho ke kontrolám vytvořeným v předchozích krocích.
10. Pokračujte u zbývajících uzlů služby WSFC. Disky vytvořené v předchozím kroku přidejte tak, že vyberete **Přidat nové zařízení**  >  **existující pevný disk**. Ujistěte se, že na všech uzlech WSFC zachováte stejné identifikátory SCSI disků.
11. Zapněte první uzel WSFC; Přihlaste se a otevřete konzolu pro správu disků (MMC). Ujistěte se, že je možné spravovat přidané sdílené disky operačním systémem a že jsou inicializovány. Naformátujte disky a přiřaďte písmeno jednotky.
12. Zapněte na ostatních uzlech služby WSFC.
13. Přidejte disk do clusteru služby WSFC pomocí **Průvodce přidáním disku** a přidejte je do sdílený svazek clusteru.
14. Otestujte převzetí služeb při selhání pomocí **Průvodce přesunutím disku** a ujistěte se, že cluster služby WSFC se sdílenými disky funguje správně.
15. Spusťte **Průvodce ověřovacím clusterem** a ověřte, zda cluster a jeho uzly fungují správně.

    Je důležité zachovat následující konkrétní položky z testu na ověření clusteru:

       - **Ověřte rezervaci trvalého prostoru úložiště**. Pokud nepoužíváte prostory úložiště s vaším clusterem (například na Azure VMware Solution síti vSAN), tento test se nedá použít. Můžete ignorovat všechny výsledky testu trvalé rezervace pro prostory úložiště, včetně tohoto upozornění. Chcete-li zabránit varování, můžete tento test vyloučit.
        
      - **Ověřte síťovou komunikaci**. Test ověření clusteru vyvolá upozornění, že je k dispozici pouze jedno síťové rozhraní na jeden uzel clusteru. Toto upozornění můžete ignorovat. Řešení Azure VMware poskytuje požadovanou dostupnost a výkon, protože uzly jsou připojené k jednomu z segmentů NSX-T. Tuto položku však nechte v rámci testu ověření clusteru, protože ověří další aspekty síťové komunikace.

16. Vytvořte pravidlo DRS k umístění virtuálních počítačů služby WSFC do stejného uzlu řešení Azure VMware. K tomu potřebujete pravidlo spřažení od hostitele k virtuálnímu počítači. Tímto způsobem se uzly clusteru spustí na stejném hostiteli řešení Azure VMware. Znovu se jedná o pilotní účely, dokud nejsou k dispozici zásady umístění.

    >[!NOTE]
    > K tomu je potřeba vytvořit lístek žádosti o podporu. Naše organizace podpory Azure vám to může pomoci.

## <a name="related-information"></a>Související informace

- [Clustering s podporou převzetí služeb při selhání ve Windows Serveru](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [Pokyny pro clusteringu Microsoft v vSphere (1037959) (vmware.com)](https://kb.vmware.com/s/article/1037959)
- [Nastavení pro clustering s podporou převzetí služeb při selhání a Clusterové služby Microsoft (vmware.com)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.mscs.doc/GUID-1A2476C0-CA66-4B80-B6F9-8421B6983808.html)
- [Síti vSAN 6,7 U3-WSFC se sdílenými disky &amp; SCSI-3 trvalé rezervace (VMware.com)](https://blogs.vmware.com/virtualblocks/2019/08/23/vsan67-u3-wsfc-shared-disksupport/)
- [Omezení řešení Azure VMware](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-vmware-solution-limits)

## <a name="next-steps"></a>Další kroky

Teď, když jste si pokryli nastavení služby WSFC v řešení Azure VMware, budete možná chtít získat informace o těchto možnostech:

- Nastavení nové služby WSFC přidáním dalších aplikací, které vyžadují funkci WSFC. Například SQL Server a SAP ASCS.
- Nastavení řešení zálohování
  - [Nastavení Azure Backup Server pro řešení Azure VMware](https://docs.microsoft.com/azure/azure-vmware/set-up-backup-server-for-azure-vmware-solution)
  - [Řešení zálohování virtuálních počítačů řešení Azure VMware](https://docs.microsoft.com/azure/azure-vmware/ecosystem-back-up-vms)
