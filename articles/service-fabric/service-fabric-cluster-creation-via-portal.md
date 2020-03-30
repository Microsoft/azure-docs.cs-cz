---
title: Vytvoření clusteru služby Service Fabric na webu Azure Portal
description: Zjistěte, jak nastavit zabezpečený cluster Service Fabric v Azure pomocí portálu Azure a trezoru klíčů Azure.
ms.topic: conceptual
ms.date: 09/06/2018
ms.openlocfilehash: 0f384da75f09390e9b0988722b974e7e16d13e63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258795"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Vytvoření clusteru Service Fabric v Azure pomocí portálu Azure
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portál Azure](service-fabric-cluster-creation-via-portal.md)
> 
> 

Toto je podrobný průvodce, který vás provede kroky nastavení clusteru Service Fabric (Linux nebo Windows) v Azure pomocí portálu Azure. Tato příručka vás provede následujícími kroky:

* Vytvořte cluster v Azure prostřednictvím portálu Azure.
* Ověřte správce pomocí certifikátů.

> [!NOTE]
> Chcete-li zobrazit pokročilejší možnosti zabezpečení, jako je ověřování uživatelů pomocí služby Azure Active Directory a nastavení certifikátů pro zabezpečení aplikací, [vytvořte cluster pomocí Správce prostředků Azure][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Zabezpečení clusteru 
Ve službě Service Fabric se k ověřování a šifrování pro zabezpečení různých aspektů clusteru a jeho aplikací využívají certifikáty. Další informace o použití certifikátů ve službě Service Fabric najdete v tématu věnovaném [scénářům zabezpečení clusteru Service Fabric][service-fabric-cluster-security].

Pokud je to poprvé, co vytváříte cluster prostředků infrastruktury služeb nebo nasazujete cluster pro testovací úlohy, můžete přeskočit na další část (**Vytvořit cluster na webu Azure Portal**) a nechat systém generovat certifikáty potřebné pro vaše clustery, které spouštějí testovací úlohy. Pokud nastavujete cluster pro produkční úlohy, pokračujte ve čtení.

#### <a name="cluster-and-server-certificate-required"></a>Certifikát clusteru a serveru (povinné)
Tento certifikát je nutný k zabezpečení clusteru a zabránění neoprávněnému přístupu k němu. Poskytuje zabezpečení clusteru několika způsoby:

* **Ověřování clusteru:** Ověřuje komunikaci mezi mezi uzlami pro federaci clusteru. Ke clusteru se mohou připojit pouze uzly, které mohou prokázat svou identitu pomocí tohoto certifikátu.
* **Ověřování serveru:** Ověří koncové body správy clusteru klientovi pro správu, takže klient pro správu ví, že mluví se skutečným clusterem. Tento certifikát také poskytuje protokol SSL pro rozhraní API pro správu protokolu HTTPS a pro průzkumníka prostředků infrastruktury služeb přes protokol HTTPS.

Aby bylo osvědčení pro tyto účely účely, musí splňovat následující požadavky:

* Certifikát musí obsahovat soukromý klíč.
* Certifikát musí být vytvořen pro výměnu klíčů, exportovatelný do souboru Výměny osobních informací (.pfx).
* **Název předmětu certifikátu se musí shodovat s doménou používanou** pro přístup k clusteru Service Fabric. To je nutné poskytnout SSL pro koncové body správy HTTPS clusteru a Service Fabric Explorer. Certifikát SSL nelze získat od certifikační autority (CA) pro doménu. `.cloudapp.azure.com` Získejte vlastní název domény pro svůj cluster. Pokud požadujete certifikát od certifikační autority, musí se název subjektu certifikátu shodovat s vlastním názvem domény používaným pro váš cluster.

#### <a name="client-authentication-certificates"></a>Certifikáty ověřování klienta
Další klientské certifikáty ověřují správce pro úlohy správy clusteru. Service Fabric má dvě úrovně přístupu: **admin** a **jen pro čtení uživatele**. Měl by být použit alespoň jednotný certifikát pro přístup pro správu. Pro další přístup na úrovni uživatele musí být poskytnut samostatný certifikát. Další informace o rolích přístupu naleznete [v tématu řízení přístupu na základě rolí pro klienty Service Fabric][service-fabric-cluster-security-roles].

Není nutné nahrát certifikáty ověření klienta do trezoru klíčů pro práci s Service Fabric. Tyto certifikáty je třeba poskytnout pouze uživatelům, kteří jsou oprávněni ke správě clusteru. 

> [!NOTE]
> Služba Azure Active Directory je doporučený způsob ověřování klientů pro operace správy clusteru. Chcete-li používat službu Azure Active Directory, musíte [vytvořit cluster pomocí Správce prostředků Azure][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certifikáty žádostí (nepovinné)
V clusteru lze z bezpečnostních důvodů nainstalovat libovolný počet dalších certifikátů. Před vytvořením clusteru zvažte scénáře zabezpečení aplikace, které vyžadují instalaci certifikátu na uzly, například:

* Šifrování a dešifrování hodnot konfigurace aplikací
* Šifrování dat mezi uzly během replikace 

Certifikáty aplikací nelze nakonfigurovat při [vytváření clusteru prostřednictvím portálu Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md). Chcete-li konfigurovat certifikáty aplikací v době instalace [clusteru,][create-cluster-arm]je nutné vytvořit cluster pomocí Správce prostředků Azure . Po vytvoření můžete také přidat certifikáty aplikací do clusteru.

## <a name="create-cluster-in-the-azure-portal"></a>Vytvoření clusteru na webu Azure Portal

Vytvoření produkčního clusteru pro potřeby vaší aplikace zahrnuje určité plánování, které vám s tím pomůže, důrazně doporučujeme přečíst a pochopit dokument [aspekty plánování clusteru Service Fabric.][service-fabric-cluster-capacity] 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Hledání prostředku clusteru Service Fabric

Přihlaste se k [portálu Azure][azure-portal].
Kliknutím **na Vytvořit prostředek** přidejte novou šablonu zdroje. Vyhledejte šablonu clusteru Service Fabric na **webu Marketplace** v části **Vše**.
V seznamu vyberte **cluster Service Fabric** Cluster.

![vyhledejte šablonu clusteru Service Fabric na webu Azure Portal.][SearchforServiceFabricClusterTemplate]

Přejděte na okno **Clusteru prostředků služby** a klepněte na tlačítko **Vytvořit**.

Okno **clusteru Create Service Fabric** má následující čtyři kroky:

### <a name="1-basics"></a>1. Základy
![Snímek obrazovky s vytvořením nové skupiny prostředků][CreateRG]

V okně Základy je třeba zadat základní podrobnosti pro váš cluster.

1. Zadejte název clusteru.
2. Zadejte **uživatelské jméno** a **heslo** pro vzdálenou plochu pro virtuální počítače.
3. Nezapomeňte vybrat **předplatné,** které chcete, aby váš cluster nasadit, zejména pokud máte více předplatných.
4. Vytvořte novou **skupinu prostředků**. Nejlepší je dát stejný název jako clusteru, protože pomáhá při jejich pozdějším hledání, zejména při pokusu o změny v nasazení nebo odstranění clusteru.
   
   > [!NOTE]
   > I když se můžete rozhodnout použít existující skupinu prostředků, je vhodné vytvořit novou skupinu prostředků. To usnadňuje odstranění clusterů a všech prostředků, které používá.
   > 
   > 
5. Vyberte **umístění,** ve kterém chcete vytvořit cluster. Pokud plánujete použít existující certifikát, který jste již odeslali do trezoru klíčů, musíte použít stejnou oblast, ve které se nachází trezor klíčů. 

### <a name="2-cluster-configuration"></a>2. Konfigurace clusteru
![Vytvoření typu uzlu][CreateNodeType]

Nakonfigurujte uzly clusteru. Typy uzlů definují velikosti virtuálních počítačů, počet virtuálních počítačů a jejich vlastnosti. Váš cluster může mít více než jeden typ uzlu, ale primární typ uzlu (první, který definujete na portálu) musí mít alespoň pět virtuálních počítačů, protože se jedná o typ uzlu, kde jsou umístěny systémové služby Service Fabric. Nekonfigurujte **vlastnosti umístění,** protože je automaticky přidána výchozí vlastnost umístění "NodeTypeName".

> [!NOTE]
> Běžným scénářem pro více typů uzlů je aplikace, která obsahuje front-endovou službu a back-endovou službu. Chcete umístit front-endslužby na menší virtuální počítače (velikosti virtuálních počítačů, jako je D2_V2) s porty otevřené k Internetu a umístit back-end služby na větší virtuální počítače (s velikostmi virtuálních počítačů, jako je D3_V2, D6_V2, D15_V2 a tak dále) bez otevřených internetových portů.
> 

1. Zvolte název typu uzlu (1 až 12 znaků obsahujících pouze písmena a čísla).
2. Minimální **velikost** virtuálních počítače pro typ primárního uzlu je řízena **úrovní odolnosti,** kterou zvolíte pro cluster. Výchozí hodnota pro úroveň odolnosti je bronzová. Další informace o odolnosti naleznete v [tématu jak zvolit odolnost clusteru Service Fabric][service-fabric-cluster-durability].
3. Vyberte **velikost virtuálního počítače**. Virtuální aplikace řady D mají jednotky SSD a jsou vysoce doporučovány pro stavové aplikace. Nepoužívejte žádnou skladovou položku virtuálního počítače, která má částečná jádra nebo má méně než 10 GB dostupné kapacity disku. Nápovědu k výběru velikosti virtuálního počítače naleznete v dokumentu o [zvážení plánování clusteru prostředků služby.][service-fabric-cluster-capacity]
4.  **Cluster s jedním uzlem a clustery tří uzlů** jsou určeny pouze pro testovací použití. Nejsou podporovány pro všechny spuštěné produkční úlohy.
5. Zvolte **kapacitu počáteční škálovací sady virtuálních** her pro typ uzlu. Můžete vertikálně navýšit nebo snížit počet virtuálních počítačů v typu uzlu později, ale na typu primárního uzlu, minimálně je pět pro produkční úlohy. Jiné typy uzlů může mít minimálně jeden virtuální virtuální počítače. Minimální **počet** virtuálních počítačů pro primární typ uzlu řídí **spolehlivost** clusteru.  
6. Konfigurace **vlastních koncových bodů**. Toto pole umožňuje zadat seznam portů oddělených čárkami, které chcete zpřístupnit prostřednictvím služby Azure Load Balancer na veřejný Internet pro vaše aplikace. Pokud například plánujete nasadit webovou aplikaci do clusteru, zadejte zde "80", abyste do clusteru povolili provoz na portu 80. Další informace o koncových bodech naleznete v [tématu komunikace s aplikacemi][service-fabric-connect-and-communicate-with-services]
7. **Povolit reverzní proxy server**.  [Reverzní proxy server Service Fabric](service-fabric-reverseproxy.md) pomáhá mikroslužbám spuštěným v clusteru Service Fabric zjišťovat a komunikovat s dalšími službami, které mají koncové body http.
8. Zpět v **okně konfigurace clusteru** v části **+Zobrazit volitelná nastavení**nakonfigurujte **diagnostiku clusteru**. Ve výchozím nastavení je v clusteru povolena diagnostika, která pomáhá při řešení problémů. Chcete-li diagnostiku zakázat, změňte přepínač **Stav** na **Vypnuto**. Vypnutí diagnostiky se **nedoporučuje.** Pokud již máte vytvořený projekt Application Insights, zadejte jeho klíč, tak, aby trasování aplikace jsou směrovány do něj.
9. **Zahrnout službu DNS**.  [Služba DNS](service-fabric-dnsservice.md) je volitelná služba, která umožňuje vyhledat další služby pomocí protokolu DNS.
10. Vyberte **režim upgradu prostředků,** na který chcete nastavit cluster. Vyberte **Automaticky**, pokud chcete, aby systém automaticky vyzvedl nejnovější dostupnou verzi a pokusil se na ni upgradovat cluster. Pokud chcete zvolit podporovanou verzi, nastavte režim na **ruční**. Další podrobnosti o režimu upgradu prostředků infrastruktury naleznete v [dokumentu Upgrade clusteru prostředků služby.][service-fabric-cluster-upgrade]

> [!NOTE]
> Podporujeme pouze clustery, které jsou spuštěny podporované verze Service Fabric. Výběrem **ručního** režimu přebíráte odpovědnost za upgrade clusteru na podporovanou verzi.
> 

### <a name="3-security"></a>3. Bezpečnost
![Snímek obrazovky s konfiguracemi zabezpečení na webu Azure Portal][BasicSecurityConfigs]

Abychom vám usnadnili nastavení zabezpečeného testovacího clusteru, poskytli jsme **základní** možnost. Pokud již certifikát máte a nahráli ho do [trezoru klíčů](/azure/key-vault/) (a povolili trezor klíčů pro nasazení), použijte možnost **Vlastní.**

#### <a name="basic-option"></a>Základní možnost
Podle obrazovek přidejte nebo znovu použijte existující trezor klíčů a přidejte certifikát. Přidání certifikátu je synchronní proces, a proto budete muset počkat na vytvoření certifikátu.

Odolejte pokušení navigace od obrazovky, dokud nebude dokončen předchozí proces.

![Vytvořit trezor CreateKeyVault]

Teď, když je trezor klíčů vytvořen, upravte zásady přístupu pro trezor klíčů. 

![Vytvořit trezor CreateKeyVault2]

Klikněte na **upravit zásady přístupu**, pak **zobrazit rozšířené zásady přístupu** a povolit přístup k virtuálním počítačům Azure pro nasazení. Doporučujeme také povolit nasazení šablony. Po provedení výběru nezapomeňte kliknout na tlačítko **Uložit** a zavřít z podokna **zásad aplikace Access.**

![Vytvořit trezor Windows3]

Zadejte název certifikátu a klepněte na tlačítko **OK**.

![Vytvořit trezor WindowsVault4]

#### <a name="custom-option"></a>Vlastní možnost
Tuto část přeskočte, pokud jste již provedli kroky v **základní** možnosti.

![Bezpečnostní možnost CustomOption]

K dokončení stránky zabezpečení potřebujete zdrojový trezor klíčů, adresu URL certifikátu a informace o kryptografickém tisku certifikátu. Pokud ho nemáte po ruce, otevřete další okno prohlížeče a na webu Azure Portal postupujte takto:

1. Přejděte ke službě trezoru klíčů.
2. Vyberte kartu "Vlastnosti" a zkopírujte id zdroje do "Zdrojového trezoru klíčů" v druhém okně prohlížeče. 

    ![CertInfo0]

3. Nyní vyberte kartu "Certifikáty".
4. Klikněte na kryptografický otisk certifikátu, který vás přenese na stránku Verze.
5. Klikněte na GUID, které vidíte pod aktuální verzí.

    ![CertInfo1]

6. Nyní byste měli být na obrazovce jako níže. Zkopírujte šestnáctkový otisk palce SHA-1 do "Kryptografického otisku certifikátu" v druhém okně prohlížeče.
7. Zkopírujte tajný identifikátor do adresy URL certifikátu v jiném okně prohlížeče.

    ![CertInfo2]

Zaškrtněte **políčko Konfigurovat upřesňující nastavení,** chcete-li zadat klientské certifikáty pro **klienta správce** a **klienta jen pro čtení**. Do těchto polí zadejte kryptografický otisk klientského certifikátu správce a pokud je k dispozici kryptografický otisk klientského certifikátu jen pro čtení. Pokud se správci pokusí připojit ke clusteru, je jim udělen přístup pouze v případě, že mají certifikát s kryptografickým otiskem, který odpovídá zde zadaným hodnotám kryptografického otisku.  

### <a name="4-summary"></a>4. Shrnutí

Nyní jste připraveni k nasazení clusteru. Než to uděláte, stáhněte si certifikát, podívejte se do velkého modrého informačního pole pro odkaz. Ujistěte se, že certifikát uchovávejte na bezpečném místě. potřebujete pro připojení ke clusteru. Vzhledem k tomu, že stažený certifikát nemá heslo, doporučujeme jej přidat.

Chcete-li dokončit vytvoření clusteru, klepněte na tlačítko **Vytvořit**. Šablonu si můžete stáhnout.

![Souhrn]

Průběh vytváření můžete sledovat v oznámeních. (Klikněte na ikonu "Zvonek" poblíž stavového řádku v pravém horním bodě obrazovky.) Pokud jste při vytváření clusteru klikli **na Připnout na úvodní obrazovku,** zobrazí se **možnost Nasazení clusteru Service Fabric** připnutého na úvodní vývěsku. **Start** Tento proces bude nějakou dobu trvat. 

Chcete-li provádět operace správy clusteru pomocí prostředí Powershell nebo CLI, musíte se připojit ke clusteru, přečtěte si další informace o tom, jak se [připojovat ke clusteru](service-fabric-connect-to-secure-cluster.md).

## <a name="view-your-cluster-status"></a>Zobrazení stavu clusteru
![Snímek obrazovky s podrobnostmi o clusteru na řídicím panelu][ClusterDashboard]

Po vytvoření clusteru můžete cluster zkontrolovat na portálu:

1. Přejděte na **procházet** a klepněte na **položku Clustery prostředků služby**.
2. Vyhledejte cluster a klikněte na něj.
3. Na řídicím panelu se zobrazí podrobnosti o clusteru, včetně veřejného koncového bodu clusteru a odkaz na Service Fabric Explorer.

Část **Sledování uzlů** na řídicím panelu clusteru označuje počet virtuálních počítačů, které jsou v pořádku a nejsou v pořádku. Další podrobnosti o stavu clusteru najdete v [úvodu modelu stavu Service Fabric][service-fabric-health-introduction].

> [!NOTE]
> Clustery Service Fabric vyžadují určitý počet uzlů, které mají být vždy až udržovat dostupnost a zachovat stav - označované jako "udržování kvora". Proto není obvykle bezpečné vypnout všechny počítače v clusteru, pokud jste nejprve provedli [úplnou zálohu stavu][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Vzdálené připojení k instanci sady škálování virtuálního počítače nebo k uzlu clusteru
Každý z NodeTypes zadáte v clusteru výsledky v nastavení sady škálování virtuálního počítače. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Další kroky
V tomto okamžiku máte zabezpečený cluster pomocí certifikátů pro ověřování správy. Dále [se připojte ke clusteru a zjistěte,](service-fabric-connect-to-secure-cluster.md) jak [spravovat tajné kódy aplikací](service-fabric-application-secret-management.md).  Další informace o [možnostech podpory service fabric](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-overview.md
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
[Vytvořit trezor CreateKeyVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[Vytvořit trezor CreateKeyVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[Vytvořit trezor Windows3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[Vytvořit trezor WindowsVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[Bezpečnostní možnost CustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Souhrn]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
