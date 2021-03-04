---
title: Replikace dat přes ExpressRoute pomocí migrace serveru Azure Migrate
description: Jak používat Azure ExpressRoute k replikaci pomocí migrace serveru Azure Migrate
author: DeSeelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: b52d61c2828ddf5c04ab943d73964d236c9017c1
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098838"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>Replikace dat přes ExpressRoute pomocí Azure Migrate: Migrace serveru

V tomto článku se dozvíte, jak nakonfigurovat [Azure Migrate: Migrace serveru](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) pro replikaci dat přes okruh ExpressRoute při migraci serverů do Azure.

## <a name="understand-azure-expressroute-circuits"></a>Vysvětlení okruhů Azure ExpressRoute
Okruh ExpressRoute (ER) spojuje vaši místní infrastrukturu s Microsoftem prostřednictvím poskytovatele připojení. Okruhy ExpressRoute se dají nakonfigurovat tak, aby používaly privátní partnerské vztahy, partnerské vztahy Microsoftu nebo obojí. Další informace o různých možnostech partnerských vztahů dostupných v ExpressRoute najdete v článku o [okruhech ExpressRoute a partnerských vztahů](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare) .

Nástroj pro migraci serveru Azure Migrate pomáhá migrovat místní servery a servery z jiných cloudů do virtuálních počítačů Azure. Tento nástroj funguje tak, že nastaví průběžný datový proud replikace pro replikaci dat ze serverů, které se mají migrovat na spravované disky ve vašem předplatném Azure. Až budete připraveni na migraci serverů, replikovaná data v Azure slouží k migraci serverů.

Data, která se replikují z vašich místních serverů, se dají nakonfigurovat tak, aby se do předplatného Azure odesílala přes Internet (pomocí zabezpečeného šifrovaného připojení) nebo přes připojení ExpressRoute. Když máte k migraci velký počet serverů, pomocí ExpressRoute pro replikaci můžete lépe migrovat servery pomocí zřízené šířky pásma dostupného pro váš okruh ExpressRoute.

V tomto článku se naučíte
> [!div class="checklist"]
>
> * Jak replikovat data pomocí okruhu ExpressRoute se soukromým partnerským vztahem.
> * Jak replikovat data pomocí okruhu ExpressRoute s partnerským vztahem Microsoftu.

## <a name="replicate-data-using-an-expressroute-circuit-with-private-peering"></a>Replikace dat pomocí okruhu ExpressRoute se soukromým partnerským vztahem

> [!NOTE]
> Replikace přes privátní okruh partnerských vztahů se aktuálně podporuje jenom pro [migraci virtuálních počítačů VMware bez agenta do Azure](./tutorial-migrate-vmware.md). Brzy bude k dispozici podpora privátních koncových bodů pro další [metody replikace](./migrate-services-overview.md#azure-migrate-server-migration-tool) .

V metodě bez agenta migrace virtuálních počítačů VMware do Azure se Azure Migrate zařízení nejdřív nahraje replikační data do účtu úložiště (účet úložiště mezipaměti) ve vašem předplatném. Replikovaná data z účtu úložiště mezipaměti se pak přesunou do disků spravovaných replikami ve vašem předplatném pomocí služby Azure Migrate. Pokud chcete pro replikaci použít privátní okruh partnerských vztahů, vytvoříte a připojíte privátní koncový bod k účtu úložiště mezipaměti. Soukromé koncové body používají jednu nebo více privátních IP adres z vaší virtuální sítě (VNet) a efektivně přinášejí účet úložiště do virtuální sítě Azure. Privátní koncový bod umožňuje zařízení Azure Migrate připojení k účtu úložiště mezipaměti pomocí privátního partnerského vztahu ExpressRoute a přenos dat přímo na privátní IP adresu. <br/>  

![Proces replikace](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
>
> - Kromě replikačních dat zařízení Azure Migrate komunikuje se službou Azure Migrate pro její činnosti na úrovni ovládacích prvků, včetně orchestrace replikace. Komunikace roviny ovládacího prvku mezi zařízením Azure Migrate a službou Azure Migrate se v rámci veřejného koncového bodu služby Azure Migrate nadále probíhají přes Internet.
> - Privátní koncový bod účtu úložiště by měl být přístupný ze sítě, ve které je Azure Migrate zařízení nasazené.
> - Služba DNS musí být nakonfigurovaná tak, aby přeložila dotazy DNS pomocí Azure Migrate zařízení pro koncový bod služby BLOB Service účtu úložiště mezipaměti do privátní IP adresy privátního koncového bodu připojeného k účtu úložiště mezipaměti.
> - Účet úložiště mezipaměti musí být přístupný na svém veřejném koncovém bodu. (Služba Azure Migrate používá veřejný koncový bod účtu úložiště mezipaměti k přesunu dat z účtu úložiště na spravované disky repliky.) 


### <a name="1-pre-requisites"></a>1. požadavky

Uživatel Azure, který vytváří privátní koncový bod, by měl mít následující oprávnění pro skupinu prostředků a virtuální síť, ve které se privátní koncový bod vytvoří.

**Případ použití** | **Oprávnění** 
--- | --- 
 Vytváření a Správa privátních koncových bodů. | Microsoft. Network/privateEndpoint/Write/Action<br/>Microsoft. Network/privateEndpoint/Read/Action  
|Připojte privátní koncový bod k virtuální síti nebo podsíti.<br/>To je vyžadováno ve virtuální síti, ve které se vytvoří privátní koncový bod.| Microsoft. Network/virtualNetworks/podsíť/spojení/akce Microsoft. Network/virtualNetworks/JOIN/Action
|Propojit soukromý koncový bod s účtem úložiště. <br/>| Microsoft. Microsoft. Storage/storageAccounts/privateEndpointConnectionApproval/Action <br/> Microsoft. Microsoft. Storage/storageAccounts/privateEndpointConnections/Read
|Vytvořte síťové rozhraní a připojte ho ke skupině zabezpečení sítě. | Microsoft. Network/networkInterfaces/Read <br/> Microsoft. Network/networkInterfaces/podsítí/Write <br/> Microsoft. Network/networkInterfaces/podsítí/čtení<br/> Microsoft. Network/networkSecurityGroups/JOIN/Action (volitelné)
Vytváření a Správa privátních zón DNS.| Role Přispěvatel Privátní DNS zóny <br/> _Ani_ <br/> Microsoft. Network/privateDnsZones/A/* <br/>  Microsoft. Network/privateDnsZones/Write Microsoft. Network/privateDnsZones/Read <br/> Microsoft. Network/privateEndpoints/privateDnsZoneGroups/Write <br/> Microsoft. Network/privateEndpoints/privateDnsZoneGroups/Read <br/> Microsoft. Network/privateDnsZones/virtualNetworkLinks/Write <br/>  Microsoft. Network/privateDnsZones/virtualNetworkLinks/Read <br/> Microsoft. Network/virtualNetworks/JOIN/Action 

### <a name="2-identify-the-cache-storage-account"></a>2. identifikace účtu úložiště mezipaměti 
 
Azure Migrate automaticky vytvoří účet úložiště mezipaměti při konfiguraci replikace (pomocí prostředí Azure Portal) pro virtuální počítač poprvé v projektu Azure Migrate. Účet úložiště se vytvoří ve stejném předplatném a skupině prostředků, ve které jste vytvořili Azure Migrate projektu.

Vytvoření a vyhledání účtu úložiště:

1. Použijte prostředí Azure Portal pro Azure Migrate k replikaci jednoho nebo více virtuálních počítačů v projektu.
2. Přejděte do skupiny prostředků projektu Azure Migrate.
3. Vyhledejte účet úložiště mezipaměti tím, že v názvu účtu úložiště identifikujete předponu **LSA** .

![Zobrazení skupiny prostředků](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
> Pokud máte ve skupině prostředků více než jeden účet úložiště s předponou **"LSA"** , můžete účet úložiště ověřit tak, že přejdete do nabídky nastavení replikace a cílová konfigurace pro kterýkoli z replikačních virtuálních počítačů v projektu. <br/> 
> ![Přehled nastavení replikace](./media/replicate-using-expressroute/storage-account.png)

### <a name="3-upgrade--cache-storage-account-to-general-purpose-v2"></a>3. upgrade účtu úložiště mezipaměti do Pro obecné účely v2 

Soukromé koncové body můžete vytvořit pouze v účtu úložiště Pro obecné účely v2 (GPv2). Pokud účet úložiště mezipaměti není účet úložiště GPv2, upgradujte ho na GPv2 pomocí následujících kroků:

1. Přejděte na svůj účet úložiště.
2. Vyberte možnost **Konfigurace**.
3. V části **druh účtu** vyberte **upgradovat**.
4. V části **Potvrdit upgrade** zadejte název svého účtu.
5. V dolní části stránky vyberte **upgrade** .

![Upgradovat účet úložiště](./media/replicate-using-expressroute/upgrade-storage-account.png)

### <a name="4-create-a-private-endpoint-for-the-storage-account"></a>4. vytvoření privátního koncového bodu pro účet úložiště

1. Přejděte na svůj účet úložiště, v nabídce vlevo vyberte **síť** a vyberte kartu **připojení privátního koncového bodu** .  
2. Vyberte **+ soukromý koncový bod**.

    a. V okně **Vytvoření privátního koncového bodu** – vyberte **předplatné** a **skupinu prostředků**. Zadejte název privátního koncového bodu a vyberte oblast účtu úložiště.  
    ![Okno Konfigurace PE](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    b. Na kartě **prostředek** zadejte **název předplatného** , ve kterém je účet úložiště. Jako **typ prostředku** vyberte **Microsoft. Storage/storageAccounts** . V části **prostředek** zadejte název účtu úložiště replikace typu GPv2. Jako **cílový dílčí prostředek** vyberte **objekt BLOB** .  
    ![storageaccountpesettings](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    c. Na kartě **Konfigurace** vyberte **virtuální síť** a **podsíť** pro privátní koncový bod účtu úložiště.  

    > [!Note]
    > Virtuální síť musí obsahovat koncový bod brány ExpressRoute nebo musí být připojená k virtuální síti pomocí brány ExpressRoute. 

    V části **privátní DNS integrace** vyberte **Ano** a integrujte ji s privátní zónou DNS. Vyberete-li možnost **Ano** , automaticky propojí zónu DNS s vybranou virtuální sítí a přidá záznamy DNS, které jsou požadovány pro překlad DNS nových IP adres a plně kvalifikované názvy domén vytvořené pro soukromý koncový bod. Přečtěte si další informace o [privátních zónách DNS.](https://docs.microsoft.com/azure/dns/private-dns-overview)

    ![privatednszone](./media/replicate-using-expressroute/private-dns-zone.png)

    d. Můžete také přidat **značky** pro privátní koncový bod.  

    e. Až se dokončí zadání podrobností, pokračujte v **kontrole a vytváření** . Po dokončení ověření vyberte **vytvořit** a vytvořte tak privátní koncový bod.

    > [!Note]
    > Pokud uživatel vytvářející soukromý koncový bod je zároveň vlastníkem účtu úložiště, soukromý koncový bod se automaticky schválí. V opačném případě musí vlastník schválit privátní koncový bod pro použití. 

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>Vytvoření privátních zón DNS a ruční přidání záznamů DNS (volitelné)

Pokud jste nevybrali možnost integrace s privátní zónou DNS v době vytváření privátního koncového bodu, použijte postup v této části k ručnímu vytvoření privátní zóny DNS. 

> [!Note]
> Pokud jste vybrali **Ano** pro integraci s privátní zónou DNS, můžete tuto část přeskočit. 

1. Vytvořte privátní zónu DNS. 

    ![createprivatedns](./media/replicate-using-expressroute/create-private-dns.png)

    a.  Na stránce **privátní DNS zóny** vyberte tlačítko **+ Přidat** a začněte vytvářet novou zónu.  
    b.  Na stránce **Vytvoření privátní zóny DNS** vyplňte požadované podrobnosti. Jako _privatelink_. blob.Core.Windows.NET zadejte název privátní zóny DNS. c. Pokračujte na kartu **Revize + vytvořit** pro kontrolu a vytvoření zóny DNS.

2. Propojit privátní zónu DNS s vaší virtuální sítí.  

    Privátní zóna DNS vytvořená výše musí být propojená s virtuální sítí, ke které je připojen soukromý koncový bod.

    a. Přejděte do privátní zóny DNS vytvořené v předchozím kroku a přejděte na odkazy virtuální sítě na levé straně stránky. Vyberte tlačítko **+ Přidat** .   
    b. Vyplňte požadované podrobnosti. Pole **odběr** a **virtuální síť** musí být vyplněna odpovídajícími podrobnostmi o virtuální síti, do které je připojen privátní koncový bod. Ostatní pole mohou být ponechána tak, jak je.

3. Dalším krokem je přidání záznamů DNS do zóny DNS. Do privátní zóny DNS přidejte položku pro plně kvalifikovaný název domény účtu úložiště.

    a. Přejděte do privátní zóny DNS a přejděte do části **Přehled** na levé straně stránky. Vyberte **+ Sada záznamů** a začněte přidávat záznamy.  

    b. Na stránce **Přidat sadu záznamů** přidejte položku pro plně kvalifikovaný název domény a soukromou IP adresu jako záznam typu.

> [!Important]
> K překladu privátních IP adres privátního koncového bodu účtu úložiště ze zdrojového prostředí můžete vyžadovat další nastavení DNS. V [tomto článku](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) se seznámíte s potřebnou konfigurací DNS.

## <a name="replicate-data-using-an-expressroute-circuit-with-microsoft-peering"></a>Replikace dat pomocí okruhu ExpressRoute s partnerským vztahem Microsoftu

Pro směrování provozu replikace přes okruh ExpressRoute, jak je znázorněno na následujícím diagramu, můžete použít partnerský vztah Microsoftu nebo stávající doménu veřejného partnerského vztahu (zastaralé pro nové připojení ExpressRoute).
![replicationwithmicrosoftpeering](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

I když replikační data přechází přes partnerský okruh Microsoftu, budete i nadále potřebovat připojení k Internetu z místní lokality pro další komunikaci (řídicí rovinu) s Azure Migrateovou službou. Existují nějaké další adresy URL, které nejsou dostupné přes ExpressRoute, že zařízení replikace nebo Hostitel Hyper-V potřebují přístup k orchestraci procesu replikace. Požadavky na adresu URL můžete zkontrolovat na základě scénáře migrace, [migrace bez agentů VMware](https://docs.microsoft.com/azure/migrate/migrate-appliance#public-cloud-urls) nebo [migrace na základě agentů](https://docs.microsoft.com/azure/migrate/migrate-replication-appliance).  

V případě, že použijete proxy server v místní lokalitě a chcete pro provoz replikace použít ExpressRoute, musíte nakonfigurovat vynechání proxy serveru pro příslušné adresy URL na místním zařízení. 

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>Konfigurace pravidel pro obejití proxy serveru na zařízení Azure Migrate (pro migrace bez agentů VMware)

1. Přihlaste se k zařízení Azure Migrate (Vzdálená plocha).   
2. Pomocí poznámkového bloku otevřete soubor C:/Složka ProgramData/MicrosoftAzure/config/appliance.js.
3. V souboru změňte řádek, který říká "EnableProxyBypassList": "false" na "EnableProxyBypassList": "true". Uložte změny a restartujte zařízení.
4. Po restartování se po otevření Správce konfigurace zařízení zobrazí možnost obejití proxy serveru v uživatelském rozhraní webové aplikace. Níže přidejte adresy URL do seznamu obcházení proxy serveru.   
    - . *. vault.azure.net
    - . *. servicebus.windows.net
    - . *. discoverysrv.windowsazure.com
    - . *. migration.windowsazure.com
    - . *. hypervrecoverymanager.windowsazure.com
    - . *. blob.core.windows.net

### <a name="configure-proxy-bypass-rules-on-the-replication-appliance-for-agent-based-migrations"></a>Konfigurace pravidel pro obcházení proxy serveru na zařízení replikace (pro migrace založené na agentech)

Pomocí následujících kroků nakonfigurujte seznam pro obcházení proxy serveru na konfiguračním serveru a procesových serverech:

1. [Stáhněte si nástroj PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) pro přístup k systémovému kontextu uživatele.
2. Spusťte aplikaci Internet Explorer v kontextu uživatele systému spuštěním následujícího příkazového řádku PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe".
3. Přidejte nastavení proxy serveru v IE.
4. V seznamu vynechat přidejte adresu URL úložiště Azure. *. blob. Core. Windows. NET.  

Výše uvedená pravidla pro obejití zajistí, že provoz replikace bude procházet přes ExpressRoute, zatímco komunikace správy může projít proxy serverem pro Internet.  

Kromě toho musíte inzerovat trasy ve filtru tras pro následující komunity protokolu BGP, aby provoz replikace Azure Migrate procházel okruhem ExpressRoute místo Internetu.  

- Oblastní komunita protokolu BGP pro zdrojovou oblast Azure (oblast projektu Azure Migrate)
- Oblastní komunita protokolu BGP pro cílovou oblast Azure (oblast pro migraci)
- Komunita protokolu BGP pro Azure Active Directory (12076:5060)

Přečtěte si další informace o [filtrech tras](https://docs.microsoft.com/azure/expressroute/how-to-routefilter-portal) a seznamu [komunit protokolu BGP pro ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp). 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [okruhech ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings).
- Přečtěte si další informace o [doménách směrování ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare).
- Přečtěte si další informace o [privátních koncových bodech](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).