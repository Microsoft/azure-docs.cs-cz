---
title: Použití Azure Migrate s privátními koncovými body
description: Pro zjišťování, hodnocení a migraci pomocí privátního propojení použijte Azure Migrate podporu privátních odkazů.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/07/2020
ms.openlocfilehash: e4feaa8f1b30bfe31f4e645943f766b5736150b3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818363"
---
# <a name="using-azure-migrate-with-private-endpoints"></a>Použití Azure Migrate s privátními koncovými body  

Tento článek popisuje, jak pomocí Azure Migrate zjišťovat, hodnotit a migrovat servery přes privátní síť pomocí [privátního propojení Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview). 

Můžete použít [Azure Migrate: Discovery and Assessment](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-discovery-and-assessment-tool) and [Azure Migrate: nástroje pro migraci serveru](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) pro připojení soukromě a bezpečně ke službě Azure Migrate prostřednictvím privátního partnerského vztahu ExpressRoute nebo lokality k připojení VPN pomocí privátního propojení Azure. 

Metoda připojení privátního koncového bodu se doporučuje, když je k dispozici organizace pro přístup k Azure Migrate službě a dalším prostředkům Azure bez procházení veřejných sítí. Podporu privátních partnerských vztahů můžete použít také k používání stávajících okruhů privátního partnerského vztahu ExpressRoute pro zajištění vyšší šířky pásma nebo čekacích požadavků. 

## <a name="support-requirements"></a>Požadavky na podporu 

### <a name="required-permissions"></a>Požadovaná oprávnění

**Přispěvatel + správce přístupu uživatele** nebo oprávnění **vlastníka** k předplatnému. 

### <a name="supported-scenarios-and-tools"></a>Podporované scénáře a nástroje

**Nasazení** | **Podrobnosti** | **Nástroje** 
--- | --- | ---
**Zjišťování a posouzení** | Provádějte bez agentů na úrovni zjišťování a hodnocení serverů, které běží na jakékoli platformě – platformy hypervisoru, jako jsou [VMware vSphere](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware) nebo [Microsoft Hyper-V](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v), veřejné cloudy, jako je například [AWS](https://docs.microsoft.com/azure/migrate/tutorial-discover-aws) nebo [GCP](https://docs.microsoft.com/azure/migrate/tutorial-discover-gcp), nebo dokonce [holé servery](https://docs.microsoft.com/azure/migrate/tutorial-discover-physical). | Azure Migrate: zjišťování a posouzení  <br/> 
**Inventář softwaru** | Seznamte se s aplikacemi, rolemi a funkcemi běžícími na virtuálních počítačích VMware. | Azure Migrate: zjišťování a posouzení  
**Vizualizace závislostí** | Využijte možnost analýzy závislostí k identifikaci a pochopení závislostí mezi servery. <br/> [Vizualizace závislostí bez agentů](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies-agentless) je nativně podporovaná Azure Migrate podpora privátních odkazů. <br/>[Vizualizace závislostí založená na agentech](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) vyžaduje připojení k Internetu. [Naučte](https://docs.microsoft.com/azure/azure-monitor/logs/private-link-security) se používat soukromé koncové body pro vizualizaci závislostí založenou na agentech. | Azure Migrate: zjišťování a posouzení |
**Migrace** | Provádějte [migrace Hyper-v bez agentů](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) nebo použijte přístup založený na agentovi k migraci [virtuálních počítačů VMware](./tutorial-migrate-vmware-agent.md), [virtuálních počítačů Hyper-V](./tutorial-migrate-physical-virtual-machines.md), [fyzických serverů](./tutorial-migrate-physical-virtual-machines.md), [virtuálních počítačů spuštěných na AWS](./tutorial-migrate-aws-virtual-machines.md), [virtuálních počítačů běžících na GCP](https://docs.microsoft.com/azure/migrate/tutorial-migrate-gcp-virtual-machines)nebo virtuálních počítačů spuštěných na jiném zprostředkovateli virtualizace. | Azure Migrate: Migrace serverů
 
>[!Note]
>
> [Migrace VMware bez agentů](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware) vyžadují přístup k Internetu nebo připojení prostřednictvím partnerského vztahu Microsoftu ExperessRoute. <br/> [Naučte](https://docs.microsoft.com/azure/migrate/replicate-using-expressroute) se, jak pomocí privátních koncových bodů provádět replikace přes privátní partnerský vztah ExpressRoute nebo připojení VPN typu Site-to-Site (S2S).  <br/><br/> 
   
#### <a name="other-integrated-tools"></a>Další integrované nástroje

Pokud je přístup k veřejné síti zakázán, některé nástroje pro migraci nemusí být schopné nahrávat data o využití do Azure Migrate projektu. Azure Migrate projekt by měl být nakonfigurovaný tak, aby umožňoval provoz ze všech sítí, aby přijímal data od jiných nabídek společnosti Microsoft nebo externího [nezávislého výrobce softwaru (ISV)](https://docs.microsoft.com/azure/migrate/migrate-services-overview#isv-integration) . 


Chcete-li povolit přístup k veřejné síti pro Azure Migrate projektu, přejděte na Azure Portal **stránce vlastností** Azure Migrate na vyberte možnost **ne** a vyberte **Uložit**.

![Diagram, který ukazuje, jak změnit režim přístupu k síti.](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>Další důležité informace   

**Důležité informace** | **Podrobnosti**
--- | --- 
**Ceny** | Informace o cenách najdete v tématu ceny [objektů BLOB v Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) a [ceny soukromého propojení Azure](https://azure.microsoft.com/pricing/details/private-link/).  
**Požadavky na virtuální síť** | Koncový bod brány ExpressRoute/VPN by se měl nacházet ve vybrané virtuální síti nebo virtuální síti, která je k ní připojená. Ve virtuální síti budete možná potřebovat 15 IP adres.  

## <a name="create-a-project-with-private-endpoint-connectivity"></a>Vytvoření projektu s připojením privátního koncového bodu

Tento [článek](https://docs.microsoft.com/azure/migrate/create-manage-projects#create-a-project-for-the-first-time) použijte k nastavení nového projektu Azure Migrate. 

> [!Note]
> U existujících projektů Azure Migrate nemůžete změnit metodu připojení na privátní koncový bod.

V části **Upřesnit** konfiguraci zadejte níže uvedené podrobnosti pro vytvoření privátního koncového bodu pro váš Azure Migrate projekt.
- V **Možnosti způsob připojení** vyberte **privátní koncový bod**. 
- V části **zakázat přístup k veřejnému koncovému bodu** ponechte výchozí nastavení  **ne**. Pokud je přístup k veřejné síti zakázán, některé nástroje pro migraci nemusí být schopné nahrávat data o využití do Azure Migrate projektu. [Další informace](#other-integrated-tools)
- V části **předplatné virtuální sítě** vyberte předplatné pro virtuální síť privátního koncového bodu. 
- V části **virtuální síť** vyberte virtuální síť pro privátní koncový bod. Zařízení Azure Migrate a další softwarové komponenty, které se musí připojit k projektu Azure Migrate musí být v této síti nebo připojené virtuální síti.
- V části **podsíť** vyberte podsíť pro soukromý koncový bod. 

Vyberte **Vytvořit**. Počkejte několik minut, než se projekt Azure Migrate nasadí. Nezavírejte tuto stránku, když probíhá vytváření projektu.

![Vytvoření projektu](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)

    
Tím se vytvoří projekt migrace, který do něj připojí soukromý koncový bod. 

## <a name="discover-and-assess-servers-for-migration-using-azure-private-link"></a>Zjišťování a vyhodnocení serverů pro migraci pomocí privátního odkazu Azure 

### <a name="set-up-the-azure-migrate-appliance"></a>Nastavení zařízení Azure Migrate 

1. V části **zjišťovat počítače**  >  **jsou vaše počítače virtualizované?** vyberte typ serveru.
2. V části **generovat Azure Migrate klíč projektu** zadejte název pro Azure Migrate zařízení. 
3. Vyberte vytvořit **klíč** a vytvořte požadované prostředky Azure. 

    > [!Important]
    > Nezavírejte stránku najít počítače během vytváření prostředků.  
    - V tomto kroku Azure Migrate vytvoří Trezor klíčů, účet úložiště, Recovery Services trezor (jenom pro migrace VMware bez agentů) a několik interních prostředků a připojí privátní koncový bod ke každému prostředku. Ve virtuální síti vybrané během vytváření projektu se vytvoří privátní koncové body.  
    - Po vytvoření privátních koncových bodů se záznamy prostředku CNAME DNS pro Azure Migrate prostředky aktualizují na alias v subdoméně s předponou ' privatelink '. Ve výchozím nastavení Azure Migrate také vytvoří privátní zónu DNS odpovídající subdoméně ' privatelink ' pro každý typ prostředku a vloží záznamy DNS pro přidružené privátní koncové body. Tím povolíte zařízením Azure Migrate a dalším softwarovým součástem umístěným ve zdrojové síti přístup k koncovým bodům prostředků Azure Migrate na privátních IP adresách.  
    - Azure Migrate taky povoluje [spravovanou identitu](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) pro projekt migrace a uděluje oprávnění spravované identitě pro zabezpečený přístup k účtu úložiště.  

4. Po úspěšném vygenerování klíče zkopírujte podrobnosti klíče a nakonfigurujte a zaregistrujte zařízení.   

#### <a name="download-the-appliance-installer-file"></a>Stažení instalačního souboru zařízení  

> [!Note]
> Pokud se vám při stahování souboru instalačního programu zařízení zobrazují problémy, vytvořte prosím případ podpory.

Azure Migrate: zjišťování a posouzení používá odlehčené Azure Migrate zařízení. Zařízení provádí zjišťování serveru a odesílá metadata konfigurace a výkonu serveru Azure Migrate.

Pokud chcete zařízení nastavit, Stáhněte si z portálu soubor ZIP obsahující instalační skript. Zkopírujte soubor zip na server, který bude hostitelem zařízení. 

Ujistěte se, že server splňuje [požadavky na hardware](https://docs.microsoft.com/azure/migrate/migrate-appliance) pro vybraný scénář (VMware/Hyper-V/fyzický nebo jiný) a že se může připojit k požadovaným cloudovým cloudům Azure – [veřejným](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity) a [státním](./migrate-appliance.md#government-cloud-urls-for-private-link-connectivity) cloudům.

Po stažení souboru ZIP spusťte skript instalačního programu, který nasadí zařízení.

#### <a name="run-the-script"></a>Spuštění skriptu

1. Extrahujte soubor zip do složky na serveru, který bude hostitelem zařízení. 
2. Na počítači spusťte PowerShell s oprávněními správce (se zvýšenými oprávněními).
3. Změňte adresář PowerShellu na složku obsahující obsah extrahovaný ze staženého souboru ZIP.
4. Spusťte skript **AzureMigrateInstaller.ps1** následujícím způsobem:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1```
   
5. Po úspěšném spuštění skriptu spustí Správce konfigurace zařízení, abyste mohli zařízení nakonfigurovat. Pokud narazíte na nějaké problémy, zkontrolujte protokoly skriptu na adrese C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>Konfigurace zařízení a spuštění průběžného zjišťování

Otevřete prohlížeč na jakémkoli počítači, který se může připojit k serveru zařízení, a otevřete adresu URL Správce konfigurace zařízení: `https://appliance name or IP address: 44368` . Alternativně můžete otevřít Configuration Manager z plochy serveru zařízení tak, že vyberete zástupce Configuration Manageru.

#### <a name="set-up-prerequisites"></a>Nastavit požadavky

1. Přečtěte si informace třetích stran a přijměte **licenční podmínky**.    
 
2. V části Configuration Manager > **nastavit požadavky** proveďte následující kroky:
   - **Připojení**: zařízení kontroluje přístup k požadovaným adresám URL. Pokud server používá proxy server:
     - Vyberte **nastavit proxy server** a zadejte adresu proxy serveru `http://ProxyIPAddress` nebo `http://ProxyFQDN` port naslouchání.
     - Pokud proxy server potřebuje přihlašovací údaje, zadejte je. Podporuje se jen proxy protokolu HTTP.
     - Pokud chcete, můžete přidat seznam adres URL/IP adres, které by měly obejít proxy server. Pokud používáte soukromý partnerský vztah ExpressRoute, ujistěte se, že tyto [adresy URL](https://docs.microsoft.com/azure/migrate/replicate-using-expressroute#configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations)obcházíte.
     - Pokud jste aktualizovali proxy server podrobnosti nebo přidané adresy URL nebo IP adresy pro obejít proxy, musíte vybrat **Uložit** .
     
        > [!Note]
        > Pokud při kontrole připojení dojde k chybě s odkazem na aka.ms/* a nechcete, aby zařízení měla přístup k této adrese URL přes Internet, musíte na zařízení zakázat službu Automatické aktualizace pomocí následujících [**kroků.**](https://docs.microsoft.com/azure/migrate/migrate-appliance#turn-off-auto-update) Po zakázání automatické aktualizace dojde k přeskočení kontroly připojení aka.ms/* adresy URL. 

   - **Čas synchronizace**: čas v zařízení by měl být synchronizovaný s internetovým časem, aby zjišťování fungovalo správně.
   - **Nainstalovat aktualizace**: zařízení zajišťuje, že jsou nainstalované nejnovější aktualizace. Po dokončení kontroly můžete vybrat **Zobrazit služby zařízení** a zobrazit stav a verze služeb spuštěných na serveru zařízení.
        > [!Note]
        > Pokud jste se rozhodli zakázat službu Automatické aktualizace na zařízení, můžete ručně aktualizovat služby zařízení, aby se získaly nejnovější verze služeb, a to podle kroků uvedených [**tady**](https://docs.microsoft.com/azure/migrate/migrate-appliance#manually-update-an-older-version).
   - **Instalace VDDK**: (_potřebné jenom pro zařízení VMware)_ zařízení kontroluje, jestli je nainstalovaná VMware vSphere Virtual disk Development Kit (VDDK). Pokud není nainstalovaný, Stáhněte si z VMware VDDK 6,7 a Extrahujte stažený obsah souboru zip do zadaného umístění na zařízení, jak je uvedeno v **pokynech k instalaci**.

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>Zaregistrujte zařízení a spusťte nepřetržité zjišťování.

Po dokončení kontroly požadovaných součástí pomocí těchto kroků zaregistrujte zařízení a spusťte nepřetržité zjišťování pro příslušné scénáře: [virtuální počítače VMware](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware#register-the-appliance-with-azure-migrate), [virtuální počítače Hyper-V](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v#register-the-appliance-with-azure-migrate), [fyzické servery](https://docs.microsoft.com/azure/migrate/tutorial-discover-physical#register-the-appliance-with-azure-migrate), [virtuální počítače s AWS](https://docs.microsoft.com/azure/migrate/tutorial-discover-aws#register-the-appliance-with-azure-migrate), virtuální počítače s [GCP](https://docs.microsoft.com/azure/migrate/tutorial-discover-gcp#register-the-appliance-with-azure-migrate).


>[!Note]
> Pokud během registrace zařízení nebo při spuštění zjišťování dochází k problémům s překladem DNS, ujistěte se, že Azure Migrate prostředky vytvořené během kroku **Generovat klíč** na portálu jsou dosažitelné z místního serveru, který hostuje Azure Migrate zařízení. [Přečtěte si další informace o tom, jak ověřit připojení k síti](#troubleshoot-network-connectivity).

### <a name="assess-your-servers-for-migration-to-azure"></a>Posouzení serverů pro migraci do Azure
Po dokončení zjišťování vyhodnoťte vaše servery ([virtuální počítače VMware](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vm), [virtuální počítače Hyper-V](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v), [fyzické servery](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vm), [virtuální](https://docs.microsoft.com/azure/migrate/tutorial-assess-aws)počítače s AWS, virtuální počítače s [GCP](https://docs.microsoft.com/azure/migrate/tutorial-assess-gcp)) pro migraci na virtuální počítače azure nebo řešení Azure VMware (AVS) pomocí nástroje Azure Migrate: Discovery and Assessment. 

Místní počítače můžete také [vyhodnotit](https://docs.microsoft.com/azure/migrate/tutorial-discover-import#prepare-the-csv) pomocí nástroje Azure Migrate: Discovery and Assessment Tool pomocí importovaného souboru hodnot oddělených čárkami (CSV).   

## <a name="migrate-servers-to-azure-using-azure-private-link"></a>Migrace serverů do Azure pomocí privátního propojení Azure

V následujících částech najdete popis kroků, které je potřeba použít Azure Migrate s [privátními koncovými body](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) pro migrace pomocí privátního partnerského vztahu ExpressRoute nebo připojení VPN.  

V tomto článku se dozvíte, jak vytvořit cestu nasazení v rámci konceptu pro replikaci založené na agentech k migraci [virtuálních počítačů VMware](./tutorial-migrate-vmware-agent.md), [virtuálních počítačů Hyper-V](./tutorial-migrate-physical-virtual-machines.md), [fyzických serverů](./tutorial-migrate-physical-virtual-machines.md), [virtuálních počítačů spuštěných na AWS](./tutorial-migrate-aws-virtual-machines.md), [virtuálních počítačů běžících na GCP](https://docs.microsoft.com/azure/migrate/tutorial-migrate-gcp-virtual-machines)nebo virtuálních počítačů spuštěných na jiném zprostředkovateli virtualizace pomocí soukromých koncových bodů Azure. Podobný postup můžete použít k provádění [migrace Hyper-V bez agentů](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) pomocí privátního odkazu.

>[!Note]
>[Migrace VMware bez agentů](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical) vyžadují přístup k Internetu nebo připojení prostřednictvím partnerského vztahu Microsoftu ExperessRoute. 

### <a name="set-up-a-replication-appliance-for-migration"></a>Nastavení zařízení replikace pro migraci 

Následující diagram znázorňuje pracovní postup replikace na základě agentů s privátními koncovými body pomocí nástroje Azure Migrate: Server pro migraci.  

![Architektura replikace](./media/how-to-use-azure-migrate-with-private-endpoints/replication-architecture.png)

Nástroj používá k replikaci serverů do Azure zařízení replikace. Pomocí tohoto článku můžete [připravit a nastavit počítač pro zařízení replikace. ](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#prepare-a-machine-for-the-replication-appliance)

Po nastavení zařízení replikace pomocí následujících pokynů vytvořte požadované prostředky pro migraci. 

1. V rámci **zjišťování počítačů**  >  **jsou vaše počítače virtualizované?** vyberte **nevirtualizované/jiné**.
2. V části **cílová oblast** vyberte a potvrďte oblast Azure, do které chcete migrovat počítače.
3. Vyberte **vytvořit prostředky** a vytvořte požadované prostředky Azure. Nezavírejte stránku během vytváření prostředků.   
    - Tím dojde k vytvoření trezoru Recovery Services na pozadí a povolíte spravovanou identitu pro trezor. Recovery Services trezor je entita, která obsahuje informace o replikaci serverů a slouží k aktivaci operací replikace.  
    - Pokud má Azure Migrate projekt připojení privátního koncového bodu, vytvoří se pro trezor Recovery Services privátní koncový bod. Tím se do privátního koncového bodu přidá pět plně kvalifikovaných privátních názvů (FQDN), jeden pro každou mikroslužbu propojenou s úložištěm Recovery Services.   
    - V tomto vzoru jsou naformátované pět názvů domén: <br/> _{Trezor-ID} – ASR-pod01-{Type}-. {Target-Geo-Code}_. privatelink.siterecovery.windowsazure.com  
    - Ve výchozím nastavení Azure Migrate automaticky vytvoří privátní zónu DNS a přidá záznamy DNS pro mikroslužby trezoru Recovery Services. Privátní zóna DNS je pak propojená s virtuální sítí privátního koncového bodu. To umožňuje místnímu zařízení replikace přeložit plně kvalifikované názvy domény na jejich privátní IP adresy.

4. Před registrací zařízení replikace zajistěte, aby plně kvalifikované názvy domén v trezoru byly dosažitelné z počítače, který je hostitelem replikačního zařízení. [Přečtěte si další informace o tom, jak ověřit připojení k síti.](#troubleshoot-network-connectivity) 

5. Jakmile ověříte připojení, Stáhněte si instalační program a soubor klíče, spusťte proces instalace a zaregistrujte zařízení pro Azure Migrate. Projděte si [Podrobné kroky](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#set-up-the-replication-appliance). Po nastavení zařízení replikace postupujte podle těchto pokynů a [nainstalujte službu mobility](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#install-the-mobility-service) na počítače, které chcete migrovat. 

### <a name="replicate-servers-to-azure-using-azure-private-link"></a>Replikace serverů do Azure pomocí privátního propojení Azure 

Teď pomocí [těchto kroků](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#replicate-machines) vyberte servery pro replikaci.  

V části **replikovat**  >  **nastavení cílového** umístění  >  **mezipaměť/replikace** klikněte na rozevírací seznam a vyberte účet úložiště, který chcete replikovat přes privátní odkaz.  

Pokud má váš Azure Migrate projekt připojení privátního koncového bodu, musíte  [udělit oprávnění ke spravované identitě Recovery Services trezoru](#grant-access-permissions-to-the-recovery-services-vault) pro přístup k účtu úložiště, který vyžaduje Azure Migrate.   

Pokud navíc chcete povolit replikaci přes privátní odkaz, [vytvořte pro účet úložiště privátní koncový bod.](#create-a-private-endpoint-for-the-storage-account-optional)

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>Udělení oprávnění k přístupu k trezoru Recovery Services

Spravovaná identita služby Recovery Services trezor vyžaduje oprávnění pro ověřený přístup k účtu úložiště mezipaměti nebo replikace. 

Pomocí níže uvedených pokynů Identifikujte Recovery Services trezor vytvořený pomocí Azure Migrate a udělte požadovaná oprávnění. 

**_Identifikace trezoru služby Recovery Services a ID objektu spravované identity_**

Podrobnosti o trezoru Recovery Services můžete najít na stránce Azure Migrate: **vlastnosti** migrace serveru. 

1. Přejít do **centra Azure Migrate** a na dlaždici Azure Migrate: Migrace serveru vyberte **Přehled** .

    ![Stránka s přehledem v centru Azure Migrate](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

2. V levém podokně vyberte možnost **vlastnosti**. Poznamenejte si název Recovery Services trezoru a ID spravované identity. Trezor bude mít _privátní koncový bod_ jako **Typ připojení** a _jiný_ jako **typ replikace**. Tyto informace budete potřebovat při poskytování přístupu k trezoru.
      
    ![Azure Migrate: Stránka vlastností migrace serveru](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

**_Udělte požadovaná oprávnění pro přístup k účtu úložiště._**

 Spravované identitě trezoru musí být udělena následující oprávnění role v účtu úložiště, který se vyžaduje pro replikaci.  V takovém případě musíte účet úložiště vytvořit předem.

>[!Note]
> Pro migraci virtuálních počítačů Hyper-V do Azure pomocí privátního propojení musíte udělit přístup k účtu úložiště replikace i k účtu úložiště mezipaměti. 

Oprávnění rolí se liší v závislosti na typu účtu úložiště.

- Účty úložiště založené na Správce prostředků (standardní typ):
  - [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor) _a_
  - [Přispěvatel dat v objektech blob služby Storage](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Účty úložiště založené na Správce prostředků (typ Premium):
  - [Přispěvatel](../role-based-access-control/built-in-roles.md#contributor) _a_
  - [Vlastník dat v objektech blob služby Storage](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

1. Přejít na účet úložiště replikace/mezipaměť vybraný pro replikaci. V levém podokně vyberte **řízení přístupu (IAM)** . 

1. V části **Přidat přiřazení role** vyberte **Přidat**:

   ![Přidat přiřazení role](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment.png)


1. Na stránce **Přidat přiřazení role** v poli **role** vyberte příslušnou roli ze seznamu oprávnění uvedeného výše. Zadejte název trezoru, který jste si poznamenali dříve, a vyberte **Uložit**.

    ![Poskytnutí přístupu na základě rolí](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment-select-role.png)

4. Kromě těchto oprávnění musíte taky dovolit přístup k důvěryhodným službám Microsoftu. Pokud je přístup k síti omezený na vybrané sítě, vyberte možnost **dovolit důvěryhodným službám Microsoftu přístup k tomuto účtu úložiště** v části **výjimky** na kartě **síť** . 

![Povoluje důvěryhodné služby Microsoftu pro účet úložiště.](./media/how-to-use-azure-migrate-with-private-endpoints/exceptions.png)


### <a name="create-a-private-endpoint-for-the-storage-account-optional"></a>Vytvoření privátního koncového bodu pro účet úložiště (volitelné)

Pokud chcete replikovat pomocí ExpressRoute se soukromým partnerským vztahem, [vytvořte privátní koncový bod](https://docs.microsoft.com/azure/private-link/tutorial-private-endpoint-storage-portal#create-storage-account-with-a-private-endpoint) pro účty úložiště mezipaměti nebo replikace (cílový podřízený prostředek: **_BLOB_**). 

>[!Note]
>
> - Soukromé koncové body můžete vytvořit pouze v účtu úložiště Pro obecné účely v2 (GPv2). Informace o cenách najdete v tématu ceny [objektů blob stránky Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) a [ceny za soukromé odkazy Azure](https://azure.microsoft.com/pricing/details/private-link/) .

Privátní koncový bod pro účet úložiště by měl být vytvořený ve stejné virtuální síti jako Azure Migrate privátní koncový bod projektu nebo jiná virtuální síť připojená k této síti. 

Vyberte **Ano** a proveďte integraci s privátní zónou DNS. Privátní zóna DNS pomáhá při směrování připojení z virtuální sítě k účtu úložiště prostřednictvím privátního propojení. Když vyberete **Ano** , automaticky propojí zónu DNS s virtuální sítí a přidá záznamy DNS pro řešení nových IP adres a plně kvalifikovaných názvů domén. Přečtěte si další informace o [privátních zónách DNS.](https://docs.microsoft.com/azure/dns/private-dns-overview)

Pokud uživatel vytvářející soukromý koncový bod je zároveň vlastníkem účtu úložiště, soukromý koncový bod se automaticky schválí. V opačném případě musí vlastník účtu úložiště schválit privátní koncový bod pro použití. Pokud chcete požadované připojení privátního koncového bodu schválit nebo odmítnout, v části **sítě** na stránce účtu úložiště klikněte na **připojení privátního koncového** bodu.

Než budete pokračovat, zkontrolujte stav stavu připojení privátního koncového bodu. 

![Stav schválení privátního koncového bodu](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

Po vytvoření privátního koncového bodu pomocí rozevírací nabídky v   >  účtu úložiště mezipaměti **Nastavení cíle** repliky  >   vyberte účet úložiště pro replikaci přes privátní odkaz.  

Ujistěte se, že místní zařízení replikace má síťové připojení k účtu úložiště na svém privátním koncovém bodu. [Přečtěte si další informace o tom, jak ověřit připojení k síti.](#troubleshoot-network-connectivity)

>[!Note]
>
> - Pro migraci virtuálních počítačů Hyper-V do Azure, pokud je účet úložiště replikace typu _Premium_ , musíte pro účet úložiště mezipaměti vybrat jiný účet úložiště _standardního_ typu. V takovém případě je nutné vytvořit privátní koncové body pro účet úložiště replikace i cache.  

Potom postupujte podle těchto pokynů [a zkontrolujte a spusťte replikaci](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#replicate-machines) a [proveďte migrace](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#run-a-test-migration).  

## <a name="troubleshoot-network-connectivity"></a>Řešení potíží s připojením k síti 

### <a name="validate-private-endpoints-configuration"></a>Ověřit konfiguraci privátních koncových bodů 

Ujistěte se, že soukromý koncový bod je schválen.  

1. Přejít na stránku Azure Migrate: stránka Vlastnosti zjišťování a posouzení a migrace serveru.
2. Stránka vlastnosti obsahuje seznam privátních koncových bodů a plně kvalifikovaných názvů privátních propojení, které byly automaticky vytvořeny nástrojem Azure Migrate.  

3. Vyberte privátní koncový bod, který chcete diagnostikovat.  
    1. Ověřte, zda je stav připojení schválen.
    2. Pokud je připojení v nedokončeném stavu, je nutné ho získat schválenou.
    3. Můžete také přejít na prostředek privátního koncového bodu a zkontrolovat, jestli virtuální síť odpovídá migraci virtuální sítě privátního koncového bodu projektu. 

    ![Zobrazit připojení privátního koncového bodu](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection.png)

### <a name="verify-dns-resolution"></a>Ověření překladu názvů DNS 

Místní zařízení (nebo zprostředkovatel replikace) přistupují k prostředkům Azure Migrate pomocí jejich plně kvalifikovaných názvů domén privátních odkazů (FQDN). K překladu privátních IP adres privátních koncových bodů ze zdrojového prostředí můžete vyžadovat další nastavení DNS. V [tomto článku](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) se seznámíte s scénáři konfigurace služby DNS, které mohou pomoct řešit problémy s připojením k síti.  

Pokud chcete ověřit připojení pomocí privátního propojení, proveďte překlad DNS koncových bodů prostředku Azure Migrate (plně kvalifikované názvy domén prostředků privátního propojení) z místního serveru hostujícího zařízení migrace a ujistěte se, že se překládá na privátní IP adresu. Informace o privátním koncovém bodu a informace o plně kvalifikovaném názvu domény prostředku jsou k dispozici na stránkách vlastností zjišťování a vyhodnocení a migrace serveru. Pokud chcete zobrazit seznam, vyberte **stáhnout nastavení DNS** .   

 ![Azure Migrate: vlastnosti zjišťování a posouzení](./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png)

 ![Azure Migrate: vlastnosti migrace serveru](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties.png)

Ilustrativní příklad pro překlad DNS pro plně kvalifikovaný název domény privátního propojení účtu úložiště  

- Zadejte _příkaz nslookup<Storage-Account-name>_. blob.Core.Windows.NET.  Nahraďte <Storage-Account-Name> názvem účtu úložiště použitým pro Azure Migrate.  

    Zobrazí se zpráva podobná této:  

   ![Příklad překladu DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- Pro účet úložiště se vrátí privátní IP adresa 10.1.0.5. Tato adresa patří do podsítě virtuální sítě privátního koncového bodu.   

Překlad DNS pro jiné Azure Migrate artefakty můžete ověřit pomocí podobného přístupu.   

Pokud je překlad DNS nesprávný, postupujte takto:  

- Pokud používáte vlastní DNS, Projděte si vlastní nastavení DNS a ověřte, jestli je konfigurace DNS správná. Pokyny najdete v tématu [Přehled privátního koncového bodu: Konfigurace DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration).
- Pokud používáte servery DNS poskytované Azure, přečtěte si následující část, kde najdete další informace k řešení potíží.  

> [!Tip]
> Můžete ručně aktualizovat záznamy DNS zdrojového prostředí úpravou souboru hostitelů DNS na místním zařízení pomocí plně kvalifikovaného názvu domény prostředků privátního propojení a jejich přidružených privátních IP adres. Tato možnost je doporučena pouze pro testování. <br/>  


### <a name="validate-the-private-dns-zone"></a>Ověřit zónu Privátní DNS   
Pokud překlad DNS nefunguje, jak je popsáno v předchozí části, může se jednat o problém se zónou Privátní DNS.  

#### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Potvrďte, že požadovaný prostředek zóny Privátní DNS existuje.  
Ve výchozím nastavení Azure Migrate také vytvoří privátní zónu DNS odpovídající subdoméně ' privatelink ' pro každý typ prostředku. Privátní zóna DNS se vytvoří ve stejné skupině prostředků Azure jako skupina prostředků privátního koncového bodu. Skupina prostředků Azure by měla obsahovat soukromé prostředky zóny DNS s následujícím formátem:
- privatelink.vaultcore.azure.net pro Trezor klíčů 
- privatelink.blob.core.windows.net pro účet úložiště
- privatelink.siterecovery.windowsazure.com pro trezor služby Recovery Services (pro replikace Hyper-V a na replikaci na základě agentů)
- privatelink.prod.migration.windowsazure.com – migrujte projekt, projekt vyhodnocení a lokalitu zjišťování.   

Privátní zóna DNS se automaticky vytvoří pomocí Azure Migrate (s výjimkou účtu úložiště mezipaměti nebo replikace vybraného uživatelem). Propojenou privátní zónu DNS můžete vyhledat tak, že přejdete na stránku privátního koncového bodu a vyberete konfigurace DNS. V části privátní integrace DNS by se měla zobrazit privátní zóna DNS. 

![Snímek konfigurace DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration.png)  

Pokud zóna DNS není k dispozici (jak je vidět níže), [vytvořte nový prostředek zóny privátní DNS.](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal)  

![Vytvoření zóny Privátní DNS](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone.png) 

#### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Potvrďte, že je zóna Privátní DNS propojená s virtuální sítí.  
Privátní zóna DNS by měla být propojená s virtuální sítí, která obsahuje privátní koncový bod pro dotaz DNS k překladu privátní IP adresy koncového bodu prostředku. Pokud privátní zóna DNS není propojená se správným Virtual Network, všechny překlady DNS z této virtuální sítě budou ignorovat privátní zónu DNS.   

V Azure Portal přejděte na prostředek privátní zóny DNS a v nabídce vlevo vyberte odkazy virtuální sítě. Měli byste vidět propojené virtuální sítě.

![Zobrazit odkazy virtuální sítě](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links.png) 

Tím se zobrazí seznam odkazů, z nichž každý má název virtuální sítě v rámci vašeho předplatného. Tady musí být uvedená virtuální síť, která obsahuje prostředek privátního koncového bodu. Jinak [podle tohoto článku](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) propojte privátní zónu DNS s virtuální sítí.    

Po propojení privátní zóny DNS s virtuální sítí budou žádosti DNS pocházející z virtuální sítě Hledat záznamy DNS v privátní zóně DNS. To je nutné pro správné překlad adres do virtuální sítě, ve které byl vytvořen privátní koncový bod.   

#### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-records"></a>Potvrďte, že privátní zóna DNS obsahuje správné záznamy. 

Přejít na soukromou zónu DNS, kterou chcete řešit. Na stránce Přehled jsou uvedeny všechny záznamy DNS pro danou privátní zónu DNS. Ověřte, zda záznam DNS pro daný prostředek existuje. Hodnotou záznamu A (IP adresa) musí být privátní IP adresa prostředků. Pokud najdete záznam A s chybnou IP adresou, je nutné odebrat chybnou IP adresu a přidat novou. Doporučuje se odebrat celý záznam a přidat nový a provést vyprázdnění DNS na místním zdrojovém zařízení.   

Ilustrativní příklad pro účet úložiště DNS záznam A v privátní zóně DNS:

![Záznamy DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-a-records.png)   

Ilustrativní příklad pro mikroslužby trezoru Recovery Services DNS záznam DNS v privátní zóně DNS: 

![Záznamy DNS pro trezor Recovery Services](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records.png)   

>[!Note]
> Když odeberete nebo upravíte záznam A, může se počítač stále překládat na starou IP adresu, protože hodnota TTL (Time to Live) pravděpodobně není ještě vypršet.  

#### <a name="other-things-that-may-affect-private-link-connectivity"></a>Další věci, které mohou mít vliv na připojení privátních linek  

Toto je nevyčerpávající seznam položek, které se dají najít v rozšířených nebo složitých scénářích: 

- Nastavení brány firewall, buď Azure Firewall připojené k virtuální síti nebo vlastní řešení brány firewall, se nasazují v počítači zařízení.  
- Partnerský vztah k síti, který může mít vliv na to, které servery DNS se používají a jakým způsobem se směruje provoz.  
- Řešení vlastní brány (NAT) můžou mít vliv na způsob směrování provozu, včetně provozu z dotazů DNS.

Další informace najdete v [Průvodci odstraňováním potíží s připojením privátního koncového bodu.](https://docs.microsoft.com/azure/private-link/troubleshoot-private-endpoint-connectivity)  

## <a name="next-steps"></a>Další kroky 
- [Dokončete proces migrace](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#complete-the-migration) a Projděte si [osvědčené postupy po migraci](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#post-migration-best-practices).