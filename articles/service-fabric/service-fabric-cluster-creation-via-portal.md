---
title: Vytvoření clusteru Service Fabric na webu Azure Portal | Dokumentace Microsoftu
description: Zjistěte, jak nastavit zabezpečení clusteru Service Fabric v Azure pomocí webu Azure portal a Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2018
ms.author: aljo
ms.openlocfilehash: f0c2108ee75f843e8285c5e2c5c55834643dc7da
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620536"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Vytvoření clusteru Service Fabric v Azure pomocí webu Azure portal
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Toto je podrobný návod, který vás provede kroky nastavení clusteru Service Fabric (s Linuxem nebo Windows) v Azure pomocí webu Azure portal. Tento průvodce vás provede následující kroky:

* Vytvoření clusteru v Azure pomocí webu Azure portal.
* Ověřování pomocí certifikátů správci.

> [!NOTE]
> Pro pokročilé možnosti zabezpečení, jako je například ověřování uživatelů pomocí Azure Active Directory a nastavení certifikátů pro zabezpečení aplikací [vytvoření clusteru pomocí Azure Resource Manageru][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Zabezpečení clusteru 
Ve službě Service Fabric se k ověřování a šifrování pro zabezpečení různých aspektů clusteru a jeho aplikací využívají certifikáty. Další informace o použití certifikátů ve službě Service Fabric najdete v tématu [scénáře zabezpečení clusteru Service Fabric][service-fabric-cluster-security].

Pokud je při prvním vytváření clusteru service fabric nebo nasazujete cluster pro testovací úlohy, můžete přeskočit k další části (**vytvořit cluster na webu Azure Portal**) a systém nechat vygenerovat certifikáty potřebné pro své clustery, na kterých běží test zatížení. Pokud jsou nastavení clusteru pro produkční úlohy a pak pokračujte ve čtení.

#### <a name="cluster-and-server-certificate-required"></a>Certifikát clusteru a serverem (povinné)
Tento certifikát je vyžadován k zabezpečení clusteru a zabránit neoprávněnému přístupu k němu. Poskytuje zabezpečení clusteru v několika způsoby:

* **Ověřování clusteru:** ověřuje komunikace mezi uzly clusteru federace. Pouze uzly, které se mohou prokázat svoji identitu pomocí tohoto certifikátu, můžete připojit ke clusteru.
* **Ověření serveru:** koncových bodů správy clusteru pro správu klienta, se ověří tak, aby klient správy ví, je komunikaci se skutečným clusterem. Tento certifikát také poskytuje zabezpečení SSL pro rozhraní API pro správu protokolu HTTPS a pro Service Fabric Explorer přes protokol HTTPS.

Chcete-li mají tyto účely, certifikát musí splňovat následující požadavky:

* Certifikát musí obsahovat privátní klíč.
* Certifikát musí být vytvořen pro výměnu klíčů, musí umožňovat export do souboru Personal Information Exchange (.pfx).
* Tento certifikát **název subjektu musí odpovídat domény** používá pro přístup ke clusteru Service Fabric. To se vyžaduje pro zajištění zabezpečení SSL pro koncové body HTTPS management a Service Fabric Explorer clusteru. Nelze získat certifikát SSL od certifikační autority (CA) pro `.cloudapp.azure.com` domény. Získání názvu vlastní domény pro váš cluster. Při žádosti o certifikát od certifikační Autority název subjektu certifikátu musí odpovídat názvu vlastní domény, použít pro váš cluster.

#### <a name="client-authentication-certificates"></a>Certifikáty pro ověřování klientů
Dalších klientských certifikátů ověřuje správci pro úlohy správy clusteru. Service Fabric má dvě úrovně přístupu: **správce** a **uživatele jen pro čtení**. Minimálně jeden certifikát pro přístup pro správu by měla sloužit. Pro další přístupu na úrovni uživatele musí být zadaná z nich jiný certifikát. Další informace o rolích přístup, najdete v části [řízení přístupu na základě rolí pro Service Fabric klienty][service-fabric-cluster-security-roles].

Není potřeba nahrát certifikáty pro ověřování klientů do služby Key Vault pro práci s platformou Service Fabric. Tyto certifikáty stačí jenom zadat pro uživatele, kteří mají oprávnění pro správu clusteru. 

> [!NOTE]
> Azure Active Directory je doporučený postup k ověřování klientů pro operace správy clusterů. Použití Azure Active Directory, je nutné [vytvoření clusteru pomocí Azure Resource Manageru][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certifikáty aplikace (volitelné)
Libovolný počet dalších certifikátů můžete nainstalovat na clusteru pro účely zabezpečení aplikace. Před vytvořením clusteru, vezměte v úvahu scénáře zabezpečení aplikací, které vyžadují certifikát nainstalovaný na uzlech, například:

* Šifrování a dešifrování hodnoty konfigurace aplikace
* Šifrování dat napříč uzly během replikace 

Certifikáty aplikace nelze nakonfigurovat, kdy [vytvořte cluster na webu Azure portal](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md). Konfigurace certifikátů aplikace během instalace clusteru, je nutné [vytvoření clusteru pomocí Azure Resource Manageru][create-cluster-arm]. Můžete také přidat certifikáty aplikace do clusteru po jeho vytvoření.

## <a name="create-cluster-in-the-azure-portal"></a>Vytvoření clusteru na portálu Azure portal

Vytváření clusteru výroby, aby splňovala potřeby vaší aplikace zahrnuje plánování, které vám pomohou s, který, důrazně doporučujeme, abyste si a pochopit, [Cluster Service Fabric aspekty plánování] [ service-fabric-cluster-capacity] dokumentu. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Vyhledejte prostředek clusteru Service Fabric

Přihlaste se na web [Azure Portal][azure-portal].
Klikněte na tlačítko **vytvořit prostředek** pro přidání nového prostředku šablony. Hledat šablony do clusteru Service Fabric **Marketplace** pod **všechno, co**.
Vyberte **Service Fabric Cluster** ze seznamu.

![Vyhledejte šablonu clusteru Service Fabric na webu Azure portal.][SearchforServiceFabricClusterTemplate]

Přejděte **Service Fabric Cluster** okna a kliknutím na **vytvořit**.

**Clusteru Service Fabric vytvořit** okno obsahuje následující čtyři kroky:

### <a name="1-basics"></a>1. Základy
![Snímek obrazovky vytváření nové skupiny prostředků.][CreateRG]

V okně základy potřebujete poskytnutí základních informací pro váš cluster.

1. Zadejte název vašeho clusteru.
2. Zadejte **uživatelské jméno** a **heslo** pro vzdálenou plochu pro virtuální počítače.
3. Je nutné vybrat **předplatné** , který má cluster k nasazení, zejména v případě, že máte více předplatných.
4. Vytvořte nový **skupiny prostředků**. Je nejlepší pro ni stejný název jako cluster, protože pomáhá při hledání je vyšší, zejména v případě, že se pokoušíte provést změny nasazení nebo odstranit svůj cluster.
   
   > [!NOTE]
   > I když se můžete rozhodnout použít existující skupinu prostředků, je vhodné vytvořit novou skupinu prostředků. To umožňuje snadno odstranit clusterů a všechny prostředky, které používá.
   > 
   > 
5. Vyberte **umístění** ve kterém chcete cluster vytvořit. Pokud máte v úmyslu použít existující certifikát, který jste už odeslali do služby key vault, je nutné použít, který službě Key vault je ve stejné oblasti. 

### <a name="2-cluster-configuration"></a>2. Konfigurace clusteru
![Vytvoření typu uzlu][CreateNodeType]

Nakonfigurujte uzly clusteru. Typy uzlů definují velikosti virtuálních počítačů, počet virtuálních počítačů a jejich vlastnosti. Cluster může mít více než jeden typ uzlu, ale primární typ uzlu (první z nich, které definujete na portálu) musí mít alespoň pět virtuálních počítačů, toto je typ uzlu, kde jsou umístěny systémové služby Service Fabric. Neprovádějte konfiguraci **vlastnosti umístění** vzhledem k tomu, že výchozí umístění vlastnost "NodeTypeName" je automaticky přidán.

> [!NOTE]
> Běžný scénář pro více typů uzlů je aplikace, který obsahuje front-endové služby a back-end služby. Chcete uvést front-endovou službu na menších virtuálních počítačů (například D2_V2 velikosti virtuálních počítačů) s otevřených portů k Internetu a back-end služby umístit na větší virtuální počítače (s velikostí virtuálních počítačů jako D3_V2 D6_V2, D15_V2 a tak dále) bez otevřených portů přístupem k Internetu.
> 

1. Zvolte název typu uzlu (1 až 12 znaků obsahující pouze písmena a číslice).
2. Minimální **velikost** virtuálních počítačů pro primární uzel typu doprovází **úroveň odolnosti** zvolíte pro cluster. Výchozí hodnotou pro úroveň odolnosti je Bronzová. Další informace o odolnosti najdete v tématu [návodu k výběru odolnosti clusteru Service Fabric][service-fabric-cluster-durability].
3. Vyberte **velikost virtuálního počítače**. Virtuální počítače řady D-series mají jednotky SSD a jsou tedy velmi vhodné pro stavové aplikace. Použít všechny skladovou Položku virtuálního počítače, který má částečné jader nebo mají méně než 10 GB kapacity disku. Odkazovat na [cluster service fabric plánovacího dokumentu zvážení] [ service-fabric-cluster-capacity] pomoc při výběru velikosti virtuálního počítače.
4.  **Jeden uzel clusteru a tři uzly clusterů** jsou určená jenom pro test. Nejsou podporovány pro všechny spuštěné úlohy v produkčním prostředí.
5. Zvolte **počáteční škálovací sady kapacitou** typu uzlu. Můžete vertikálně navýšit nebo snížit počet virtuálních počítačů typu později, ale na primární typ uzlu, minimální hodnota je pět pro produkční úlohy. Jiné typy uzlů může mít minimálně jeden virtuální počítač. Minimální **číslo** virtuálních počítačů pro jednotky typ primárního uzlu **spolehlivost** vašeho clusteru.  
6. Konfigurace **vlastní koncové body**. Toto pole můžete zadat čárkou oddělený seznam portů, které chcete zveřejnit prostřednictvím služby Azure Load Balancer do veřejného Internetu pro vaše aplikace. Pokud máte v plánu nasadit webovou aplikaci do clusteru, zadejte například "80" zde pro povolení provozu na portu 80 do vašeho clusteru. Další informace o koncových bodech najdete v tématu [komunikaci s aplikacemi][service-fabric-connect-and-communicate-with-services]
7. **Povolit reverzní proxy server**.  [Service Fabric reverzní proxy server](service-fabric-reverseproxy.md) spuštěného v clusteru Service Fabric mikroslužeb pomáhá zjistit a komunikovat s ostatními službami, které mají koncových bodů http.
8. Zpátky **konfigurace clusteru** okně v části **+ Zobrazit volitelná nastavení**, konfigurace clusteru **diagnostiky**. Ve výchozím nastavení je povolená Diagnostika ve vašem clusteru jako pomoc při řešení potíží. Pokud chcete zakázat diagnostiku změnit **stav** přepnutím **vypnout**. Když vypnete diagnostiku je **není** nedoporučuje. Pokud již jste vytvořili projekt Application Insights a pak zadejte svůj klíč tak, aby trasování aplikací jsou směrovány do ní.
9. **Zahrnout službu DNS**.  [Služba DNS](service-fabric-dnsservice.md) volitelnou službu, která umožňuje najít další služby pomocí protokolu DNS.
10. Vyberte **režim upgradu prostředků infrastruktury** , které chcete nastavit váš cluster. Vyberte **automatické**, pokud má systém automaticky vyzvednutí na nejnovější dostupnou verzi a zkuste cluster upgradovat na ni. Nastavte režim na **ruční**, pokud chcete zvolit podporovanou verzi. Pro další informace o prostředcích infrastruktury upgradu viz režim [Upgrade clusteru Service Fabric dokumentu.][service-fabric-cluster-upgrade]

> [!NOTE]
> Podporujeme pouze clustery, na kterých běží podporované verze systému Service Fabric. Výběrem **ruční** režimu, můžete je možné na odpovědnost cluster upgradovat na podporovanou verzi.
> 

### <a name="3-security"></a>3. Zabezpečení
![Snímek obrazovky s konfigurací zabezpečení na webu Azure portal.][BasicSecurityConfigs]

Pro usnadnění nastavení zabezpečené testovací cluster za vás, uvádíme **základní** možnost. Pokud už máte certifikát a nahrát ho do vašeho [služby key vault](/azure/key-vault/) (a povoleny služby key vault pro nasazení), pak použít **vlastní** možnost

#### <a name="basic-option"></a>Tarif Basic
Postupujte podle obrazovky pro přidání nebo opětovné použití existujícího trezoru klíčů a přidání certifikátu. Přidání certifikátu je synchronního procesu, a proto budete muset počkat certifikát, který chcete vytvořit.

Odolejte pokušení navigaci pryč z obrazovky, dokud se nedokončí předchozí proces.

![CreateKeyVault]

Teď, když se vytvoří trezor klíčů, upravte zásady přístupu pro trezor klíčů. 

![CreateKeyVault2]

Klikněte na **upravit zásady přístupu**, pak **zobrazit pokročilé zásady přístupu** a povolit přístup k Azure Virtual Machines pro nasazení. Doporučujeme, abyste povolili šablonu nasazení. Po provedení výběru, nezapomeňte kliknout **Uložit** tlačítko a zavřete z celkového počtu **zásady přístupu** podokně.

![CreateKeyVault3]

Zadejte název certifikátu a klikněte na tlačítko **OK**.

![CreateKeyVault4]

#### <a name="custom-option"></a>Vlastní možnost
Tuto část přeskočte, pokud jste už provedli kroky v **základní** možnost.

![SecurityCustomOption]

Potřebujete zdrojový trezor klíčů, adresy URL certifikátu a informací o kryptografickém otisku certifikátu k dokončení na stránku zabezpečení. Pokud ho nemáte po ruce, otevřete další okno prohlížeče a webu Azure Portal postupujte následovně

1. Přejděte k vaší službě key vault.
2. Vyberte kartu "Properties" a zkopírovat ID prostředku "Zdrojový trezor klíčů" ostatní okna prohlížeče 

    ![CertInfo0]

3. Teď vyberte kartu "Certifikáty".
4. Klikněte na kryptografický otisk certifikátu, která vás přesměruje na stránku verze.
5. Kliknutím na identifikátory GUID se zobrazí v aktuální verzi.

    ![CertInfo1]

6. Teď byste měli být na obrazovce jako níže. Zkopírujte šestnáctkové kryptografický otisk SHA-1 "Kryptografický otisk certifikátu" ostatní okna prohlížeče
7. Zkopírujte identifikátor"tajný klíč" do "URL certifikátu" ostatní okna prohlížeče.

    ![CertInfo2]

Zkontrolujte **konfigurovat rozšířená nastavení** pole k zadání klientské certifikáty pro **klient pro správu** a **klienta jen pro čtení**. V těchto polích zadejte kryptografický otisk certifikátu klienta správce a kryptografický otisk klientského certifikátu uživatele jen pro čtení, pokud je k dispozici. Když správci došlo k pokusu o připojení ke clusteru, získají přístup pouze v případě, že mají certifikát s kryptografickým otiskem, který odpovídá hodnoty kryptografického otisku tady zadáte.  

### <a name="4-summary"></a>4. Souhrn

Nyní jste připraveni k nasazení clusteru. Předtím, než to uděláte, stáhněte si certifikát, hledejte uvnitř velké modrou informační pole odkazu. Ujistěte se, aby certifikátu na bezpečném místě. budete potřebovat k přihlášení ke clusteru. Protože certifikát, který jste si stáhli nemá žádné heslo, se doporučuje přidat jeden.

Vytvoření clusteru, kliknutím na **vytvořit**. Volitelně si můžete stáhnout šablony.

![Souhrn]

Průběh vytváření můžete sledovat v oznámeních. (Klikněte na ikonu zvonku u stavového řádku v pravém horním rohu obrazovky.) Pokud jste při vytváření clusteru klikli na **Připnout na Úvodní panel**, na tabuli **Start** bude připnuté **Nasazování clusteru Service Fabric**. Tento proces bude chvíli trvat. 

Abyste mohli provádět operace správy ve vašem clusteru pomocí Powershellu nebo rozhraní příkazového řádku, budete muset připojit ke clusteru, přečtěte si informace o tom, jak na [připojení k vašemu clusteru](service-fabric-connect-to-secure-cluster.md).

## <a name="view-your-cluster-status"></a>Zobrazení stavu clusteru
![Snímek obrazovky okna Podrobnosti o clusteru na řídicím panelu.][ClusterDashboard]

Po vytvoření clusteru si můžete prohlédnout na portálu pro váš cluster:

1. Přejděte na **Procházet** a klikněte na tlačítko **clustery Service Fabric**.
2. Vyhledejte svůj cluster a klikněte na něj.
3. Na řídicím panelu se zobrazí podrobnosti o clusteru, včetně veřejného koncového bodu clusteru a odkaz na Service Fabric Explorer.

**Uzlu monitorování** části v okně řídicí panel clusteru označuje počet virtuálních počítačů, které jsou v pořádku a není v pořádku. Můžete najít další podrobnosti o stavu clusteru v [Úvod modelu health Service Fabric][service-fabric-health-introduction].

> [!NOTE]
> Clustery Service Fabric vyžadují určitý počet uzlů nahoru vždy zachování dostupnosti a zachovat stav – označuje jako "Správa kvora". Proto není obvykle bezpečné vypnutí všech počítačů v clusteru, pokud jste nejdřív provedli [úplného zálohování vašeho státu][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Vzdálené připojení k instanci virtuálního počítače Škálovací sady nebo uzel clusteru.
Každá z NodeTypes zadáte ve výsledcích clusteru do sady škálování virtuálního počítače získávání nastavení. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Další postup
V tomto okamžiku máte zabezpečeného clusteru s použitím certifikátů pro ověřování pro správu. Dále [připojení k vašemu clusteru](service-fabric-connect-to-secure-cluster.md) a zjistěte, jak [Správa tajných klíčů aplikací](service-fabric-application-secret-management.md).  Také se dozvíte o [možnosti podpory pro Service Fabric](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-get-started.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[BasicSecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/BasicSecurityConfigs.PNG
[CreateKeyVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[CreateKeyVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[CreateKeyVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[CreateKeyVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[SecurityCustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Souhrn]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
