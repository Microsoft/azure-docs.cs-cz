---
title: Vytvoření clusteru služby Service Fabric na webu Azure Portal
description: Přečtěte si, jak nastavit zabezpečený Service Fabric cluster v Azure pomocí Azure Portal a Azure Key Vault.
ms.topic: conceptual
ms.date: 09/06/2018
ms.openlocfilehash: c679a804db09b1034f31e9d8da1f7d2ad206f684
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90563722"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Vytvoření clusteru Service Fabric v Azure pomocí Azure Portal
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Toto je podrobný průvodce, který vás provede kroky nastavení clusteru Service Fabric (Linux nebo Windows) v Azure pomocí Azure Portal. Tato příručka vás provede následujícími kroky:

* Vytvořte v Azure cluster pomocí Azure Portal.
* Ověřování správců pomocí certifikátů

> [!NOTE]
> Pro pokročilejší možnosti zabezpečení, jako je ověřování uživatelů pomocí Azure Active Directory a nastavení certifikátů pro zabezpečení aplikací, [vytvořte cluster pomocí Azure Resource Manager][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Zabezpečení clusteru 
Ve službě Service Fabric se k ověřování a šifrování pro zabezpečení různých aspektů clusteru a jeho aplikací využívají certifikáty. Další informace o použití certifikátů ve službě Service Fabric najdete v tématu věnovaném [scénářům zabezpečení clusteru Service Fabric][service-fabric-cluster-security].

Pokud cluster Service Fabric vytváříte poprvé nebo nasazujete cluster pro testovací úlohy, můžete přejít k další části (**vytvořit cluster v Azure Portal**) a nechat systém vygenerovat certifikáty potřebné pro clustery, které spouštějí testovací úlohy. Pokud nastavujete cluster pro produkční úlohy, pak pokračujte ve čtení.

#### <a name="cluster-and-server-certificate-required"></a>Certifikát clusteru a serveru (povinné)
Tento certifikát je nutný k zabezpečení clusteru a zabránění neoprávněnému přístupu k němu. Zabezpečení clusteru zajišťuje několik způsobů:

* **Ověřování clusteru:** Ověřuje komunikaci mezi uzly a federačním clusterem. Do clusteru se mohou připojit pouze uzly, které mohou prokázat svoji identitu pomocí tohoto certifikátu.
* **Ověřování serveru:** Ověřuje koncové body správy clusteru pro klienta pro správu, takže klient pro správu ví, že komunikuje s skutečným clusterem. Tento certifikát taky poskytuje TLS pro rozhraní API pro správu HTTPS a pro Service Fabric Explorer přes HTTPS.

Aby mohl certifikát plnit tyto účely, musí splňovat následující požadavky:

* Certifikát musí obsahovat privátní klíč.
* Certifikát musí být vytvořen pro výměnu klíčů, který lze exportovat do souboru. pfx (Personal Information Exchange).
* **Název subjektu certifikátu se musí shodovat s doménou** používanou pro přístup ke clusteru Service Fabric. Tento postup je nutný k poskytnutí TLS pro koncové body správy HTTPS clusteru a Service Fabric Explorer. Certifikát TLS/SSL nemůžete od certifikační autority (CA) pro doménu získat `.cloudapp.azure.com` . Získejte vlastní název domény pro svůj cluster. Když vyžádáte certifikát od certifikační autority, název subjektu certifikátu se musí shodovat s názvem vlastní domény použitým pro váš cluster.

#### <a name="client-authentication-certificates"></a>Certifikáty pro ověřování klientů
Další certifikáty klientů ověřují správci pro úlohy správy clusteru. Service Fabric má dvě úrovně přístupu: **správce** a **uživatel s oprávněním jen pro čtení**. Je třeba použít minimálně jeden certifikát pro přístup pro správu. Pro další přístup na úrovni uživatele je třeba zadat samostatný certifikát. Další informace o přístupových rolích najdete v tématu [řízení přístupu na základě rolí pro klienty Service Fabric][service-fabric-cluster-security-roles].

Pro Key Vault pro práci s Service Fabric není nutné nahrávat certifikáty pro ověřování klientů. Tyto certifikáty je třeba poskytnout pouze uživatelům, kteří mají oprávnění ke správě clusteru. 

> [!NOTE]
> Azure Active Directory je doporučený způsob ověřování klientů pro operace správy clusterů. Pokud chcete použít Azure Active Directory, musíte [cluster vytvořit pomocí Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Certifikáty aplikací (volitelné)
V clusteru je možné nainstalovat libovolný počet dalších certifikátů pro účely zabezpečení aplikací. Před vytvořením clusteru Vezměte v úvahu scénáře zabezpečení aplikací, které vyžadují, aby byl v uzlech nainstalován certifikát, například:

* Šifrování a dešifrování hodnot konfigurace aplikace
* Šifrování dat napříč uzly během replikace 

Certifikáty aplikací nelze konfigurovat při [vytváření clusteru prostřednictvím Azure Portal](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md). Chcete-li nakonfigurovat certifikáty aplikací v době instalace clusteru, je nutné [vytvořit cluster pomocí Azure Resource Manager][create-cluster-arm]. Do clusteru můžete přidat také certifikáty aplikace po jeho vytvoření.

## <a name="create-cluster-in-the-azure-portal"></a>Vytvoření clusteru v Azure Portal

Vytvoření produkčního clusteru, který splňuje požadavky vaší aplikace, zahrnuje nějaké plánování. Doporučujeme vám to, abyste si přečetli dokument [důležité informace o plánování Service Fabric clusteru][service-fabric-cluster-capacity] a porozuměli jim. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Vyhledejte prostředek Service Fabric clusteru.

Přihlaste se na [Azure Portal][azure-portal].
Kliknutím na **vytvořit prostředek** přidejte novou šablonu prostředků. Na **webu Marketplace** vyhledejte šablonu Service Fabric clusteru v rámci **všeho**.
V seznamu vyberte **Cluster Service Fabric** .

![Vyhledejte Service Fabric šablonu clusteru v Azure Portal.][SearchforServiceFabricClusterTemplate]

Přejděte do okna **Cluster Service Fabric** a klikněte na **vytvořit**.

Okno **vytvořit Service Fabric clusteru** má následující čtyři kroky:

### <a name="1-basics"></a>1. základní informace
![Snímek obrazovky s vytvořením nové skupiny prostředků][CreateRG]

V okně základy musíte zadat základní podrobnosti pro svůj cluster.

1. Zadejte název clusteru.
2. Pro virtuální počítače zadejte **uživatelské jméno** a **heslo** pro vzdálenou plochu.
3. Ujistěte se, že jste vybrali **předplatné** , do kterého chcete cluster nasadit, zejména v případě, že máte více předplatných.
4. Vytvořte novou **skupinu prostředků**. Doporučujeme mu dát stejný název jako cluster, protože ho později pomůže najít, zejména v případě, že se pokoušíte provést změny nasazení nebo odstranit cluster.
   
   > [!NOTE]
   > I když se můžete rozhodnout použít existující skupinu prostředků, je dobrým zvykem vytvořit novou skupinu prostředků. Díky tomu můžete snadno odstranit clustery a všechny prostředky, které používá.
   > 
   > 
5. Vyberte **umístění** , ve kterém chcete cluster vytvořit. Pokud plánujete použít existující certifikát, který jste už nahráli do trezoru klíčů, musíte použít stejnou oblast, ve které je váš Trezor klíčů. 

### <a name="2-cluster-configuration"></a>2. konfigurace clusteru
![Vytvoření typu uzlu][CreateNodeType]

Nakonfigurujte uzly clusteru. Typy uzlů definují velikosti virtuálních počítačů, počet virtuálních počítačů a jejich vlastnosti. Cluster může mít více než jeden typ uzlu, ale typ primárního uzlu (první uzel, který definujete na portálu) musí mít aspoň pět virtuálních počítačů, protože se jedná o typ uzlu, kde jsou umístěné systémové služby Service Fabric. Nekonfigurujte **vlastnosti umístění** , protože je automaticky přidána výchozí vlastnost umístění pro "NodeType".

> [!NOTE]
> Běžným scénářem pro více typů uzlů je aplikace, která obsahuje front-end službu a back-endové služby. Chcete front-end službu umístit na menší virtuální počítače (velikosti virtuálních počítačů, jako je D2_V2), porty otevřené na internetu a umístit back-end službu na větší virtuální počítače (s velikostmi virtuálních počítačů, jako jsou D3_V2, D6_V2, D15_V2 a tak dále), bez otevřených internetových portů.
> 

1. Vyberte název typu uzlu (1 až 12 znaků obsahujících jenom písmena a čísla).
2. Minimální **Velikost** virtuálních počítačů pro typ primárního uzlu je řízena **úrovní odolnosti** , kterou zvolíte pro cluster. Výchozí hodnota pro úroveň odolnosti je bronzová. Další informace o odolnosti najdete v tématu [Jak zvolit Service Fabric odolnost clusteru][service-fabric-cluster-durability].
3. Vyberte **Velikost virtuálního počítače**. Virtuální počítače řady D-Series mají jednotky SSD a jsou velmi doporučené pro stavové aplikace. Nepoužívejte žádné skladové položky virtuálních počítačů, které mají částečné jádra nebo mají méně než 10 GB dostupné kapacity disku. Nápovědu k výběru velikosti virtuálního počítače najdete v [dokumentu aspekty plánování clusteru Service Fabric][service-fabric-cluster-capacity] .
4.  **Cluster s jedním uzlem a clustery se třemi uzly** jsou určeny pouze pro testovací použití. Nepodporují se pro žádné spuštěné provozní úlohy.
5. Vyberte **počáteční kapacitu sady škálování virtuálního počítače** pro typ uzlu. Počet virtuálních počítačů v typu uzlu můžete později škálovat nebo rozmístit, ale na primárním typu uzlu je minimum pět pro produkční úlohy. Jiné typy uzlů můžou mít minimálně jeden virtuální počítač. Minimální **počet** virtuálních počítačů pro typ primárního uzlu zařídí **spolehlivost** clusteru.  
6. Nakonfigurujte **vlastní koncové body**. Toto pole umožňuje zadat čárkami oddělený seznam portů, které chcete zpřístupnit prostřednictvím Azure Load Balancer k veřejnému Internetu pro vaše aplikace. Pokud například plánujete nasadit webovou aplikaci do clusteru, zadejte sem "80", čímž povolíte provoz na portu 80 do clusteru. Další informace o koncových bodech najdete v tématu [komunikace s aplikacemi][service-fabric-connect-and-communicate-with-services] .
7. **Povolte reverzní proxy**.  [Service Fabric reverzní proxy](service-fabric-reverseproxy.md) pomáhá mikroslužbám běžícím v clusteru Service Fabric zjišťovat a komunikovat s dalšími službami, které mají koncové body http.
8. Zpátky v okně **Konfigurace clusteru** v části **+ Zobrazit volitelná nastavení** nakonfigurujte **diagnostiku** clusteru. Ve výchozím nastavení jsou diagnostika ve vašem clusteru zapnutá, aby pomohla řešit problémy. Pokud chcete zakázat diagnostiku, změňte **stav** přepínač na **vypnuto**. Vypnutí diagnostiky se **nedoporučuje.** Pokud již máte vytvořený projekt Application Insights, přiřaďte jeho klíč, aby se do něj směrovalo trasování aplikace.
9. **Zahrnout službu DNS**.  [Služba DNS](service-fabric-dnsservice.md) nabízí volitelnou službu, která vám umožní najít další služby pomocí protokolu DNS.
10. Vyberte **režim upgradu prostředků infrastruktury** , pro který chcete nastavit cluster. Vyberte možnost **automaticky**, pokud chcete, aby systém automaticky vybral nejnovější dostupnou verzi a pokusil se upgradovat cluster na něj. Nastavte režim na **Ruční**, pokud chcete zvolit podporovanou verzi. Další podrobnosti o režimu upgradu prostředků infrastruktury najdete v [dokumentu Service Fabric upgrade clusteru.][service-fabric-cluster-upgrade]

> [!NOTE]
> Podporujeme jenom clustery, na kterých běží podporované verze Service Fabric. Když vyberete **Ruční** režim, převezmete zodpovědnost za upgrade clusteru na podporovanou verzi.
> 

### <a name="3-security"></a>3. zabezpečení
![Snímek obrazovky s konfiguracemi zabezpečení na Azure Portal.][BasicSecurityConfigs]

Aby bylo možné nastavit zabezpečený testovací cluster snadno pro vás, nabízíme **základní** možnost. Pokud už máte certifikát a nahráli jste ho do [trezoru klíčů](../key-vault/index.yml) (a povolili jste Trezor klíčů pro nasazení), použijte **vlastní** možnost.

#### <a name="basic-option"></a>Možnost Basic
Podle obrazovky přidejte nebo znovu použijte existující Trezor klíčů a přidejte certifikát. Přidání certifikátu je synchronní proces, takže budete muset počkat na vytvoření certifikátu.

Odolat pokušeníi přechodu z obrazovky, dokud se předchozí proces nedokončí.

![Snímek obrazovky se stránkou zabezpečení se zvoleným základním zobrazením v podokně trezoru klíčů a vytvoření trezoru klíčů.][CreateKeyVault]

Teď, když je vytvořený Trezor klíčů, upravte zásady přístupu pro svůj Trezor klíčů. 

![Snímek obrazovky se zobrazí v podokně vytvořit Service Fabric clusteru s vybranou možností 3 zabezpečení a vysvětlením, že Trezor klíčů není povolený.][CreateKeyVault2]

Klikněte na **upravit zásady přístupu** a pak na **Zobrazit zásady pokročilého přístupu** a povolte přístup k Azure Virtual Machines pro nasazení. Doporučuje se také povolit nasazení šablony. Po provedení výběru nezapomeňte kliknout na tlačítko **Uložit** a zavřít podokno **zásady přístupu** .

![Snímek obrazovky se zobrazí v podokně vytvořit Service Fabric clusteru s otevřeným podoknem zabezpečení a otevře se podokno zásady přístupu.][CreateKeyVault3]

Zadejte název certifikátu a klikněte na **OK**.

![Snímek obrazovky se zobrazí podokno vytvořit Service Fabric clusteru se zabezpečením vybraným jako dříve, ale bez vysvětlení, že Trezor klíčů není povolený.][CreateKeyVault4]

#### <a name="custom-option"></a>Vlastní možnost
Pokud jste již provedli kroky uvedené v možnosti **základní** , přeskočte tuto část.

![Snímek obrazovky se zobrazí v dialogovém okně Konfigurace nastavení zabezpečení clusteru.][SecurityCustomOption]

K dokončení stránky zabezpečení potřebujete trezor zdrojového klíče, adresu URL certifikátu a informace o kryptografickém otisku certifikátu. Pokud ho nemáte k dispozici, otevřete jiné okno prohlížeče a v Azure Portal proveďte následující postup:

1. Přejděte do služby trezoru klíčů.
2. Vyberte kartu Vlastnosti a zkopírujte ID prostředku do pole "úložiště klíčů" v jiném okně prohlížeče. 

    ![Snímek obrazovky ukazuje okno Vlastnosti pro Trezor klíčů.][CertInfo0]

3. Teď vyberte kartu certifikáty.
4. Klikněte na kryptografický otisk certifikátu, který vás přesměruje na stránku verze.
5. Klikněte na identifikátory GUID, které vidíte v aktuální verzi.

    ![Snímek obrazovky se zobrazeným oknem certifikátu pro Trezor klíčů][CertInfo1]

6. Nyní byste měli být na obrazovce, například níže. Zkopírovat šestnáctkový kryptografický otisk SHA-1 do "kryptografického otisku certifikátu" v jiném okně prohlížeče
7. Zkopírujte klíč tajného kódu do adresy URL certifikátu v jiném okně prohlížeče.

    ![Snímek obrazovky se zobrazí v dialogovém okně verze certifikátu s možností kopírování identifikátoru certifikátu.][CertInfo2]

Zaškrtněte pole **Konfigurovat upřesňující nastavení** a zadejte klientské certifikáty pro klienta **správce** a **klienta jen pro čtení**. V těchto polích zadejte kryptografický otisk certifikátu klienta správce a kryptografický otisk certifikátu uživatele, který je jen pro čtení, pokud je k dispozici. Když se správci pokusí připojit ke clusteru, udělí se jim přístup jenom v případě, že mají certifikát s kryptografickým otiskem, který odpovídá hodnotám kryptografického otisku, které tady zadáte.  

### <a name="4-summary"></a>4. souhrn

Teď jste připraveni nasadit cluster. Před tím, než to provedete, Stáhněte certifikát, vyhledejte ho v rámci velkého modrého informačního pole pro daný odkaz. Nezapomeňte certifikát uchovávat na bezpečném místě. potřebujete ho připojit k vašemu clusteru. Vzhledem k tomu, že certifikát, který jste stáhli, nemá heslo, doporučujeme, abyste ho přidali.

Vytvoření clusteru dokončíte kliknutím na **vytvořit**. Volitelně můžete šablonu stáhnout.

![Snímek obrazovky se stránkou pro vytvoření souhrnu clusteru Service Fabric s odkazem pro zobrazení a stažení certifikátu.][Summary]

Průběh vytváření můžete sledovat v oznámeních. (Klikněte na ikonu zvonku u stavového řádku v pravém horním rohu obrazovky.) Pokud jste při vytváření clusteru klikli **na Připnout na úvodní panel** , uvidíte, že se na **úvodní** desce připnulo **nasazení Service Fabric clusteru** . Tento proces bude nějakou dobu trvat. 

Aby bylo možné provádět operace správy v clusteru pomocí PowerShellu nebo rozhraní příkazového řádku, musíte se připojit ke clusteru. Další informace o tom, jak se připojit [ke clusteru](service-fabric-connect-to-secure-cluster.md), najdete v tématu.

## <a name="view-your-cluster-status"></a>Zobrazení stavu clusteru
![Snímek obrazovky s podrobnostmi o clusteru na řídicím panelu][ClusterDashboard]

Po vytvoření clusteru můžete na portálu zkontrolovat cluster:

1. Přejděte na **Procházet** a klikněte na **Service Fabric clustery**.
2. Vyhledejte svůj cluster a klikněte na něj.
3. Na řídicím panelu se zobrazí podrobnosti o clusteru, včetně veřejného koncového bodu clusteru a odkaz na Service Fabric Explorer.

Oddíl **monitorování uzlu** v okně řídicího panelu clusteru indikuje počet virtuálních počítačů, které jsou v pořádku a nejsou v pořádku. Další podrobnosti o stavu clusteru najdete v [Service Fabric Úvod do modelu stavu][service-fabric-health-introduction].

> [!NOTE]
> Clustery Service Fabric vyžadují, aby byl při zachování dostupnosti a zachování stavu, který se označuje jako "Údržba kvora", vždy určitý počet uzlů. Proto obvykle není bezpečné vypnout všechny počítače v clusteru, pokud jste nejprve neprováděli [úplné zálohování vašeho stavu][service-fabric-reliable-services-backup-restore].
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Vzdálené připojení k instanci sady škálování virtuálního počítače nebo uzlu clusteru
Každé z NodeType zadaných v clusteru má za následek nastavování sady škálování virtuálního počítače. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Další kroky
V tomto okamžiku máte zabezpečený cluster s použitím certifikátů pro ověřování správy. Pak se [připojte ke svému clusteru](service-fabric-connect-to-secure-cluster.md) a Naučte se [spravovat tajné klíče aplikace](service-fabric-application-secret-management.md).  Přečtěte si také informace o [možnostech podpory Service Fabric](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: /powershell/azure/
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/general/overview.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
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
[Summary]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
