---
title: Vytvořit cluster Service Fabric na portálu Azure | Microsoft Docs
description: Tento článek popisuje postup nastavení zabezpečení clusteru Service Fabric v Azure pomocí portálu Azure a Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/09/2018
ms.author: chackdan
ms.openlocfilehash: 864952554b04d8d6bc14aad92d168298e554710e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Vytvořit cluster Service Fabric v Azure pomocí portálu Azure
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Toto je podrobný průvodce, který vás provede kroky nastavení clusteru Service Fabric (Linux nebo Windows) v Azure pomocí portálu Azure. Tento průvodce vás provede následující kroky:

* Vytvořte cluster v Azure prostřednictvím portálu Azure.
* Ověřování pomocí certifikátů správci.

> [!NOTE]
> Pro pokročilejší možnosti zabezpečení, jako je například ověřování uživatelů s Azure Active Directory a nastavení certifikátů pro zabezpečení aplikací [vytvořit cluster pomocí Azure Resource Manager][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Zabezpečení clusteru 
Ve službě Service Fabric se k ověřování a šifrování pro zabezpečení různých aspektů clusteru a jeho aplikací využívají certifikáty. Další informace o tom, jak certifikáty se používají v Service Fabric najdete v tématu [scénáře zabezpečení clusteru Service Fabric][service-fabric-cluster-security].

Pokud je to poprvé vytváříte service fabric cluster nebo nasazení clusteru s podporou pro testovací úlohy, můžete přeskočit k další části (**clusteru vytvořit na portálu Azure**) a musí mít systém vygenerujete certifikáty potřebné pro clusterů se systémem testovací úlohy. Pokud jsou nastavení clusteru s podporou pro úlohy v produkčním prostředí, můžete pokračovat ve čtení.

#### <a name="cluster-and-server-certificate-required"></a>Cluster a server certifikátu (povinné)
Tento certifikát je vyžadován k zabezpečení clusteru a zabránit neoprávněnému přístupu k němu. Poskytuje zabezpečení clusteru několik způsoby:

* **Ověřování clusteru:** ověřuje komunikaci mezi uzly pro cluster federaci. Pouze uzly, které můžete prokázání své identity s tímto certifikátem může připojit ke clusteru.
* **Ověření serveru:** ověřuje koncové body správy clusteru klient pro správu, tak, aby klient správy zná je rozhovoru s skutečné clusteru. Tento certifikát také poskytuje SSL pro rozhraní API pro správu protokolu HTTPS a pro Service Fabric Explorer přes protokol HTTPS.

Poskytovat tyto účely, certifikát musí splňovat následující požadavky:

* Certifikát musí obsahovat privátní klíč.
* Certifikát se musí vytvořit pro výměnu klíčů, exportovat do souboru Personal Information Exchange (.pfx).
* Tento certifikát **název předmětu musí odpovídat domény** používá pro přístup ke clusteru Service Fabric. To je potřeba zajistit SSL pro koncové body správy protokolu HTTPS a Service Fabric Explorer clusteru. Nelze získat certifikát SSL od certifikační autority (CA) pro `.cloudapp.azure.com` domény. Získejte vlastní název domény pro váš cluster. Při žádosti o certifikát od certifikační Autority název subjektu certifikátu musí odpovídat vlastní název domény pro váš cluster používá.

#### <a name="client-authentication-certificates"></a>Certifikáty pro ověřování klientů
Další klientské certifikáty ověřit správci pro úlohy správy clusteru. Service Fabric má dvě úrovně přístupu: **správce** a **jen pro čtení uživatele**. Minimálně je třeba použít jeden certifikát pro přístup pro správu. Pro další přístupu na úrovni uživatele je třeba zadat samostatný certifikát. Další informace o přístupu rolí najdete v tématu [řízení přístupu na základě rolí pro Service Fabric klienty][service-fabric-cluster-security-roles].

Není nutné nahrát certifikáty pro ověřování klientů pro Key Vault pro práci s Service Fabric. Tyto certifikáty pouze musí být zadané pro uživatele, kteří mají oprávnění pro správu clusteru. 

> [!NOTE]
> Azure Active Directory je doporučeným způsobem k ověřování klientů pro operace správy clusterů. Chcete-li používat Azure Active Directory, je potřeba [vytvořit cluster pomocí Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certifikáty aplikace (volitelné)
Libovolný počet dalších certifikátů lze nainstalovat na clusteru pro aplikace z bezpečnostních důvodů. Před vytvořením clusteru, vezměte v úvahu scénáře zabezpečení aplikací, které vyžadují certifikát nainstalovaný na uzlech, například:

* Šifrování a dešifrování hodnot konfigurace aplikace
* Šifrování dat mezi uzly během replikace 

Při vytváření clusteru prostřednictvím portálu Azure se nedá nakonfigurovat certifikáty k aplikaci. Konfigurace aplikace certifikáty během instalace clusteru, musíte [vytvořit cluster pomocí Azure Resource Manager][create-cluster-arm]. Certifikáty aplikace můžete také přidat do clusteru po jeho vytvoření.

< /a "Vytvoření cluster-portal" ></a>

## <a name="create-cluster-in-the-azure-portal"></a>Vytvoření clusteru na portálu Azure

Vytváření clusteru výroby podle vašich potřeb aplikace zahrnuje některé plánování, které vám pomohou s který, důrazně doporučujeme přečíst si a pochopit [Cluster Service Fabric aspekty plánování] [ service-fabric-cluster-capacity] dokumentu. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Vyhledejte prostředek clusteru Service Fabric
![vyhledávání pro šablonu clusteru Service Fabric na portálu Azure.][SearchforServiceFabricClusterTemplate]

1. Přihlaste se na web [Azure Portal][azure-portal].
2. Klikněte na tlačítko **vytvořit prostředek** o přidání nového prostředku šablony. Vyhledejte šablony Service Fabric Cluster **Marketplace** pod **všechno, co**.
3. Vyberte **Service Fabric Cluster** ze seznamu.
4. Přejděte na **Service Fabric Cluster** okně klikněte na tlačítko **vytvořit**,
5. **Cluster Service Fabric vytvořit** okno obsahuje následující čtyři kroky:

#### <a name="1-basics"></a>1. Základy
![Snímek obrazovky vytváření nové skupiny prostředků.][CreateRG]

V okně základy budete muset zadat základní informace pro váš cluster.

1. Zadejte název clusteru.
2. Zadejte **uživatelské jméno** a **heslo** pro vzdálené plochy pro virtuální počítače.
3. Je nutné vybrat **předplatné** má cluster pro nasazení do, zvláště pokud máte více předplatných.
4. Vytvoření **novou skupinu prostředků**. Je nejlepší a použít stejný název jako cluster, protože pomáhá při hledání je později, zejména v případě, že se pokoušíte provést změny vašeho nasazení nebo odstranit cluster.
   
   > [!NOTE]
   > I když můžete se rozhodnete použít existující skupinu prostředků, je vhodné vytvořit novou skupinu prostředků. To usnadňuje odstranit clustery a všechny prostředky, které používá.
   > 
   > 
5. Vyberte **oblast** ve kterém chcete vytvořit cluster. Pokud máte v úmyslu použít stávající certifikát, který jste již odeslali do trezoru klíčů, musíte použít stejné oblasti, kterou Key vault je v. 

#### <a name="2-cluster-configuration"></a>2. Konfigurace clusteru
![Vytvoření typu uzlu][CreateNodeType]

Nakonfigurujte uzly clusteru. Typy uzlů definovat velikosti virtuálních počítačů, počet virtuálních počítačů a jejich vlastnosti. Cluster může mít více než jeden typ uzlu, ale typ primárního uzlu (první, kterou definujete na portálu) musí mít aspoň pět virtuálních počítačů, jedná se o typ uzlu, kde jsou umístěny Service Fabric systémových služeb. Nekonfigurujte **vlastnosti umístění** vzhledem k tomu je automaticky přidá výchozí umístění vlastnost "NodeTypeName".

> [!NOTE]
> Běžný scénář pro více typů uzlu je aplikace, která obsahuje službu front-endu a back endové službě. Chcete umístit front-endová služba menší virtuálních počítačů (velikosti virtuálních počítačů jako D2_V2) s porty otevřené k Internetu a umístí back endové službě větší virtuální počítače (s velikostí virtuálního počítače jako D3_V2, D6_V2, D15_V2 a tak dále) bez internetového portů otevřete.
> 
> 

1. Zvolte název pro váš typ uzlu (obsahující pouze písmena a čísla 1 až 12 znaků).
2. Minimální **velikost** virtuálních počítačů pro primární uzel typu vycházejí z **odolnost** vrstvy, které zvolíte pro cluster. Výchozí hodnota pro úroveň odolnosti je Bronzová. Další informace o odolnost, najdete v části [jak zvolit odolnost clusteru Service Fabric][service-fabric-cluster-durability].
3. Vyberte velikost virtuálního počítače. Virtuální počítače D-series jednotek SSD a jsou doporučovány stavových aplikací. Použít všechny virtuálních počítačů SKU, který má částečné jader nebo mají méně než 10 GB kapacity disku. Odkazovat na [cluster service fabric plánování aspekt dokumentu] [ service-fabric-cluster-capacity] pro pomoc při výběru velikost virtuálního počítače.
4. Zvolte počet virtuálních počítačů pro typ uzlu. Můžete škálovat nahoru nebo dolů počet virtuálních počítačů v uzlu typu později, ale na primárním uzlu, který typ minimum je 5 pro úlohy v produkčním prostředí. Ostatní typy uzlů může mít minimálně jeden virtuální počítač. Minimální **číslo** virtuálních počítačů pro typ jednotky primárního uzlu **spolehlivost** clusteru.  
5. **Jeden uzel clusteru a tři uzlu clusterů** -ty jsou určené pouze pro testovací. Některé funkce nejsou podporovány pro všechny spuštěné úlohy v produkčním prostředí.
6. Nakonfigurujte vlastní koncové body. Toto pole umožňuje zadat seznam portů, které chcete zpřístupnit prostřednictvím nástroje pro vyrovnávání zatížení Azure do veřejného Internetu pro vaše aplikace oddělených čárkami. Například pokud plánujete nasadit webovou aplikaci pro váš cluster, zadejte "80" zde Pokud chcete povolit přenosy na portu 80 do clusteru. Další informace o koncových bodů najdete v tématu [komunikaci s aplikacemi][service-fabric-connect-and-communicate-with-services]
7. Konfigurace clusteru **diagnostiky**. Ve výchozím nastavení jsou diagnostiky povolené v clusteru, vám pomůže při řešení potíží. Pokud chcete zakázat diagnostiky změnu **stav** přepnutím **vypnout**. Vypnutí možnosti diagnostiky je **není** nedoporučuje. Pokud jste už vytvořených projektů Application Insights a pak zadejte svůj klíč, aby trasování aplikací jsou směrovány do něj.
8. Vyberte režim upgradu prostředků infrastruktury, které chcete nastavit váš cluster. Vyberte **automatické**, pokud chcete, aby systém automaticky vyzvedne, až na nejnovější dostupnou verzi a pokuste se upgrade clusteru na ni. Nastavit režim **ruční**, pokud chcete zvolit podporovanou verzi. Pro další informace o prostředcích infrastruktury upgradu naleznete v režimu [service fabric clusteru upgrade dokumentu.][service-fabric-cluster-upgrade]

> [!NOTE]
> Podporujeme pouze clustery, které běží podporovaná verze služby prostředků infrastruktury. Výběrem **ruční** režimu přenášíte na odpovědnost cluster upgradovat na podporovanou verzi. > 
> 

#### <a name="3-security"></a>3. Zabezpečení
![Snímek obrazovky konfigurace zabezpečení na portálu Azure.][BasicSecurityConfigs]

Chcete-li nastavení zabezpečení zkušební cluster snadno pro vás, uvádíme **základní** možnost. Pokud už máte certifikát a mít nahrán do vaší keyvault (a povolené trezor klíčů pro nasazení), použijte **vlastní** možnost

##### <a name="basic-option"></a>Základní možnosti
Postupujte podle obrazovky pro přidání nebo znovu použít existující keyvault a přidání certifikátu. Přidání certifikátu je synchronní proces, a proto je nutné čekat na certifikát, který chcete vytvořit.


Odolejte riziko navigace směrem od obrazovky až do dokončení procesu předchozí.

![CreateKeyVault]

Teď, když je certifikát přidat do vaší keyvault, zobrazí se následující obrazovka, požadavkem, abyste upravit zásady přístupu pro váš Keyvault. Klikněte na **upravit zásady přístupu pro.** .

![CreateKeyVault2]

Klikněte na Upřesnit zásady a povolit přístup k virtuálním počítačům pro nasazení. Doporučujeme, abyste povolili také nasazení šablony. Po provedení váš výběr, nezapomeňte znovu klikněte na tlačítko **Uložit** tlačítko a zavřete mimo **zásady přístupu** podokně.

![CreateKeyVault3]

Nyní jste připraveni pokračovat s ostatními procesu vytvoření clusteru.

![CreateKeyVault4]

##### <a name="custom-option"></a>Vlastní možnost
Tuto část přeskočit, pokud jste již provedli kroky v **základní** možnost.

![SecurityCustomOption]

Musíte CertificateThumbprint, SourceVault a CertificateURL informace k dokončení stránce zabezpečení. Pokud jste měli, otevře další okno prohlížeče a proveďte následující kroky


1. Přejděte do vaší keyvault, vyberte certifikát. 
2. Vyberte kartu "vlastnosti" a zkopírujte do "Zdroje Key vault" v okně prohlížeče jiné ID prostředku 

    ![CertInfo0]

3. Nyní vyberte kartu "Certifikáty".
4. Klikněte na kryptografický otisk certifikátu, který přejdete na stránku verze.
5. Klikněte na identifikátory GUID, najdete v části aktuální verze.

    ![CertInfo1]

6. Nyní byste měli na obrazovce jako níže. Zkopírujte do "Kryptografický otisk certifikátu" na ostatní okna prohlížeče 'kryptografický otisk.
7. Zkopírujte informace "Tajný klíč identifikátor" na "Certifikát adresu" na jiné okno prohlížeče.


![CertInfo2]


Zkontrolujte **konfigurovat upřesňující nastavení** pole k zadání klientské certifikáty pro **správce klienta** a **jen pro čtení klienta**. V těchto polích zadejte kryptografický otisk certifikátu klienta správce a kryptografický otisk certifikátu klienta uživatele jen pro čtení, pokud je k dispozici. Když se správce pokusí připojit ke clusteru, získají přístup pouze v případě, že mají certifikát s kryptografickým otiskem, který odpovídá hodnoty kryptografického otisku zadali tady.  

#### <a name="4-summary"></a>4. Souhrn

Nyní jste připraveni k nasazení clusteru. Předtím, než můžete udělat, stáhněte si certifikát, vyhledejte uvnitř velké modré informační pole odkazu. Ujistěte se, aby certifikát na bezpečném místě. je nutné se připojit ke clusteru. Vzhledem k tomu, že certifikát, který jste stáhli nemá heslo, doporučujeme, abyste přidali jeden.

Chcete-li dokončit vytvoření clusteru, klikněte na tlačítko **vytvořit**. Volitelně si můžete stáhnout šablony. 

![Souhrn]

Průběh vytváření můžete sledovat v oznámeních. (Klikněte na ikonu zvonku u stavového řádku v pravém horním rohu obrazovky.) Pokud jste při vytváření clusteru klikli na **Připnout na Úvodní panel**, na tabuli **Start** bude připnuté **Nasazování clusteru Service Fabric**.

Aby bylo možné provádět operace správy v clusteru pomocí prostředí Powershell nebo rozhraní příkazového řádku, budete muset připojit ke clusteru, další informace o tom, jak v [připojení ke clusteru](service-fabric-connect-to-secure-cluster.md).

### <a name="view-your-cluster-status"></a>Zobrazení stavu clusteru
![Snímek obrazovky podrobnosti o clusteru v řídicím panelu.][ClusterDashboard]

Po vytvoření clusteru si můžete prohlédnout cluster v portálu:

1. Přejděte na **Procházet** a klikněte na tlačítko **clustery infrastruktury služby**.
2. Vyhledejte cluster a klikněte na něj.
3. Na řídicím panelu se zobrazí podrobnosti o clusteru, včetně veřejného koncového bodu clusteru a odkaz na Service Fabric Explorer.

**Uzlu monitorování** část v okně řídicí panel clusteru označuje počet virtuálních počítačů, které jsou v pořádku a není v pořádku. Můžete najít další podrobnosti o stavu clusteru na [Service Fabric stavu modelu ÚVOD][service-fabric-health-introduction].

> [!NOTE]
> Clusterů Service Fabric vyžadovat určité počet uzlů na být až vždycky zachování dostupnosti a zároveň zachovat stav – označuje jako "Správa kvora". Proto je obvykle není bezpečné vypnout všechny počítače v clusteru, pokud jste provedli nejprve [úplného zálohování vaší stavu][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Vzdálené připojení k instanci a sadu škálování virtuálního počítače nebo uzlu clusteru
Každý z NodeTypes zadáte v clusteru výsledky do sady škálování virtuálního počítače získávání nastavení. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Další postup
V tuto chvíli máte zabezpečené cluster pro správu ověřování pomocí certifikátů. Dále [připojit ke clusteru](service-fabric-connect-to-secure-cluster.md) a zjistěte, jak [spravovat tajné klíče aplikace](service-fabric-application-secret-management.md).  Také další informace o [možnosti podpory Service Fabric](service-fabric-support.md).

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
<!--[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md#remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node -->
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
