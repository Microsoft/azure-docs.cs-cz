---
title: ASP.NET kontejnerů aplikací Azure; Kontejnery a migrace ASP.NET aplikací do Azure Kubernetes.
description: 'Kurz: kontejnerizace & migrujte ASP.NET aplikace do služby Azure Kubernetes Service.'
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 3/2/2021
ms.author: rahugup
ms.openlocfilehash: ffc97984a335b72a3aa8c8d8cca65a3fddf7af38
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780731"
---
# <a name="containerize-aspnet-applications-and-migrate-to-azure-kubernetes-service"></a>Kontejnerizace ASP.NET aplikace a migrace do služby Azure Kubernetes Service

V tomto článku se naučíte, jak kontejnerizace aplikace ASP.NET a migrovat je do [služby Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) pomocí nástroje Azure Migrate: vytvoření kontejneru aplikace. Proces kontejneru nevyžaduje přístup k vašemu základu kódu a poskytuje snadný způsob, jak kontejnerizace stávající aplikace. Nástroj pracuje s použitím stavu spuštění aplikací na serveru k určení komponent aplikace a pomáhá je zabalit v imagi kontejneru. Kontejnerová aplikace se pak dá nasadit ve službě Azure Kubernetes (AKS).

Azure Migrate: Nástroj pro vykontejneru aplikace v tuto chvíli podporuje –

- Uzavření ASP.NET aplikace a nasaďte je do kontejnerů Windows na AKS.
- Uzavření Java Web Apps na Apache Tomcat (na serverech se systémem Linux) a nasaďte je do kontejnerů Linux v AKS. [Další informace](./tutorial-containerize-java-kubernetes.md)


Azure Migrate: Nástroj pro vykontejneruování aplikací vám pomůže

- **Zjištění aplikace**: nástroj se vzdáleně připojuje k aplikačním serverům, na kterých běží aplikace ASP.NET, a zjišťuje součásti aplikace. Nástroj vytvoří souboru Dockerfile, který se dá použít k vytvoření image kontejneru pro aplikaci.
- **Sestavení image kontejneru**: můžete zkontrolovat a dále upravit souboru Dockerfile podle požadavků vaší aplikace a použít ho k sestavení image kontejneru aplikace. Obrázek kontejneru aplikace je vložen do vámi zadaného Azure Container Registry.
- **Nasazení do služby Azure Kubernetes**: Nástroj pak vygeneruje soubory YAML definice prostředků Kubernetes potřebné k nasazení kontejnerové aplikace do vašeho clusteru Azure Kubernetes Service. Soubory YAML můžete přizpůsobit a použít je k nasazení aplikace na AKS.

> [!NOTE]
> Azure Migrate: Nástroj pro kontejnery aplikací vám pomůže najít konkrétní typy aplikací (webové aplikace v ASP.NET a Java Web Apps na Apache Tomcat) a jejich komponenty na aplikačním serveru. Pokud chcete zjišťovat servery a inventář aplikací, rolí a funkcí, které běží na místních počítačích, použijte Azure Migrate: funkce zjišťování a posouzení. [Další informace](./tutorial-discover-vmware.md)

I když všechny aplikace nebudou využívat přímý posun na kontejnery bez významného přemístění, některé výhody přesunu stávajících aplikací do kontejnerů bez přepsání zahrnují:

- **Vylepšené využití infrastruktury:** Pomocí kontejnerů může více aplikací sdílet prostředky a hostovat se na stejné infrastruktuře. To vám může pomoci konsolidovat infrastrukturu a zlepšit využití.
- **Zjednodušená správa:** Hostováním aplikací na moderní platformě spravované infrastruktury, jako je AKS, můžete zjednodušit postupy správy a přitom si zachovat kontrolu nad infrastrukturou. Toho můžete dosáhnout vyřazením nebo omezením procesů údržby a správy infrastruktury, které se tradičně provádějí s vlastní infrastrukturou.
- **Přenositelnost aplikace:** Díky zvýšenému přijetí a standardizaci formátů specifikace kontejneru a platforem orchestrace už není přenositelnost aplikací obavy.
- **Přijímají moderní správu pomocí DevOps:** Pomůže vám přijmout a standardizovat moderní postupy pro správu a zabezpečení pomocí infrastruktury jako kódu a přechodu do DevOps.


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte účet Azure.
> * Nainstalujte nástroj Azure Migrate: kontejner aplikace.
> * Objevte svou aplikaci ASP.NET.
> * Sestavte image kontejneru.
> * Nasaďte aplikaci s kontejnerem na AKS.

> [!NOTE]
> Kurzy vám ukážou nejjednodušší cestu nasazení pro scénář, abyste mohli rychle nastavit zkušební verzi. V těchto kurzech se v rámci možností používají jen výchozí možnosti a neuvádějí se všechny varianty nastavení ani všechny cesty.

## <a name="prerequisites"></a>Předpoklady

Než začnete s tímto kurzem, musíte mít splněné následující požadavky:

**Požadavek** | **Podrobnosti**
--- | ---
**Určení počítače pro instalaci nástroje** | Počítač s Windows, který se má nainstalovat a spustit Azure Migrate: Nástroj pro vytvoření kontejneru aplikace Počítač s Windows může být server (Windows Server 2016 nebo novější) nebo operační systém klienta (Windows 10), což znamená, že nástroj může běžet i na ploše. <br/><br/> Počítač s Windows, na kterém nástroj běží, by měl mít síťové připojení k serverům nebo virtuálním počítačům hostujícím kontejnerizované aplikace ASP.NET.<br/><br/> Zajistěte, aby na počítači s Windows, na kterém běží Azure Migrate: Nástroj pro vyřazení aplikací pro ukládání artefaktů aplikací, bylo k dispozici 6 GB volného místa. <br/><br/> Tento počítač s Windows by měl mít přístup k internetu, a to buď přímo, nebo prostřednictvím proxy serveru. <br/> <br/>Nainstalujte nástroj Microsoft Nasazení webu na počítač, na kterém běží pomocný nástroj kontejneru aplikace a aplikační server, pokud ještě není nainstalovaný. Nástroj si můžete stáhnout [tady](https://aka.ms/webdeploy3.6) .
**Aplikační servery** | Povolit vzdálenou komunikaci PowerShellu na aplikačních serverech: Přihlaste se k aplikačnímu serveru a postupujte podle [těchto](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting) pokynů, abyste mohli zapnout vzdálenou komunikaci PowerShellu. <br/><br/> Pokud aplikační server používá systém Windows Server 2008 R2, ujistěte se, že je na aplikačním serveru nainstalováno prostředí PowerShell 5,1. Pokud si chcete stáhnout a nainstalovat PowerShell 5,1 na aplikační server, postupujte [podle pokynů.](https://docs.microsoft.com/powershell/scripting/windows-powershell/wmf/setup/install-configure) <br/><br/> Nainstalujte nástroj Microsoft Nasazení webu na počítač, na kterém běží pomocný nástroj kontejneru aplikace a aplikační server, pokud ještě není nainstalovaný. Nástroj si můžete stáhnout [tady](https://aka.ms/webdeploy3.6) .
**Aplikace ASP.NET** | Nástroj aktuálně podporuje <br/><br/> -ASP.NET aplikace pomocí rozhraní Microsoft .NET Framework 3,5 nebo novějšího.<br/> – Aplikační servery se systémem Windows Server 2008 R2 nebo novějším (aplikační servery by měly používat PowerShell verze 5,1). <br/> -Aplikace spuštěné v Internetová informační služba (IIS) 7,5 nebo novější. <br/><br/> Nástroj aktuálně nepodporuje <br/><br/> – Aplikace vyžadující ověřování systému Windows (AKS nepodporuje aktuálně gMSA). <br/> – Aplikace, které závisí na dalších službách systému Windows hostovaných mimo službu IIS.


## <a name="prepare-an-azure-user-account"></a>Příprava uživatelského účtu Azure

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

Po nastavení předplatného budete potřebovat uživatelský účet Azure s:
- Oprávnění vlastníka k předplatnému Azure
- Oprávnění k registraci Azure Active Directorych aplikací

Pokud jste si právě vytvořili bezplatný účet Azure, jste vlastníkem vašeho předplatného. Pokud nejste vlastníkem předplatného, pracujte s vlastníkem a přiřaďte oprávnění následujícím způsobem:

1. V Azure Portal vyhledejte "předplatná" a v části **služby** vyberte **předplatná**.

    ![Vyhledávací pole, ve kterém se má hledat předplatné Azure](./media/tutorial-discover-vmware/search-subscription.png)

2. Na stránce **předplatná** vyberte předplatné, ve kterém chcete vytvořit projekt Azure Migrate.
3. V předplatném vyberte **řízení přístupu (IAM)**  >  **Kontrola přístupu**.
4. V části **kontrolovat přístup** vyhledejte příslušný uživatelský účet.
5. V nabídce **Přidat přiřazení role** klikněte na **Přidat**.

    ![Vyhledejte uživatelský účet pro kontrolu přístupu a přiřazení role.](./media/tutorial-discover-vmware/azure-account-access.png)

6. V části **Přidat přiřazení role** vyberte roli vlastníka a v našem příkladu vyberte účet (azmigrateuser). Potom klikněte na **Uložit**.

    ![Otevře stránku přidat přiřazení role, která účtu přiřadí roli.](./media/tutorial-discover-vmware/assign-role.png)

7. Váš účet Azure taky potřebuje **oprávnění k registraci aplikací Azure Active Directory.**
8. V Azure Portal přejděte na   >    >  **uživatelské nastavení** Azure Active Directory uživatelé.
9. V **nastavení uživatele** ověřte, že uživatelé Azure AD můžou registrovat aplikace (ve výchozím nastavení nastavené na **Ano** ).

      ![Ověřte v uživatelských nastaveních, která můžou uživatelé registrovat v aplikacích Active Directory.](./media/tutorial-discover-vmware/register-apps.png)

10. Pokud je nastavení ' Registrace aplikací ' nastaveno na hodnotu ' ne ', požádejte tenanta/globálního správce, aby přiřadil požadované oprávnění. Alternativně může tenant nebo globální správce přiřadit roli **vývojářů aplikace** k účtu, který umožňuje registraci aplikace Azure Active Directory. [Další informace](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).


## <a name="download-and-install-azure-migrate-app-containerization-tool"></a>Stažení a instalace Azure Migrate: Nástroj pro zabalení aplikace

1. [Stáhněte](https://go.microsoft.com/fwlink/?linkid=2134571) si Azure Migrate: Instalační program kontejneru aplikace na počítači s Windows.
2. Spusťte PowerShell v režimu správce a změňte adresář PowerShellu na složku, která obsahuje instalační program.
3. Spusťte instalační skript pomocí příkazu.

   ```powershell
   .\AppContainerizationInstaller.ps1
   ```

## <a name="launch-the-app-containerization-tool"></a>Spustit nástroj pro kontejnery aplikací

1. Otevřete prohlížeč na jakémkoli počítači, který se může připojit k počítači s Windows, na kterém běží Nástroj pro vytvoření kontejneru aplikací, a otevřete adresu URL nástroje: **https://*název počítače nebo IP adresa*: 44368**.

   Alternativně můžete aplikaci otevřít z plochy tak, že vyberete zástupce aplikace.

2. Pokud se zobrazí upozornění s oznámením, že vaše připojení není soukromé, klikněte na Upřesnit a vyberte možnost pokračovat na web. Toto upozornění se zobrazí, protože webové rozhraní používá certifikát TLS/SSL podepsaný svým držitelem.
3. Na přihlašovací obrazovce použijte místní účet správce na počítači, abyste se mohli přihlásit.
4. V poli zadat typ aplikace vyberte **ASP.NET Web Apps** jako typ aplikace, kterou chcete kontejnerizace.

    ![Výchozí načtení nástroje pro vytváření kontejnerů aplikace](./media/tutorial-containerize-apps-aks/tool-home.png)


### <a name="complete-tool-pre-requisites"></a>Dokončení nástrojů – předpoklady
1. Přijměte **licenční podmínky** a přečtěte si informace třetích stran.
6. Ve webové aplikaci nástroje > **nastavit požadavky** proveďte následující kroky:
   - **Připojení**: nástroj kontroluje, zda má počítač se systémem Windows přístup k Internetu. Pokud počítač používá proxy server:
     - Klikněte na **nastavit proxy server** a zadejte adresu proxy serveru (ve formě IP adresy nebo plně kvalifikovaného názvu domény) a portu pro naslouchání.
     - Pokud proxy server potřebuje přihlašovací údaje, zadejte je.
     - Podporuje se jen proxy protokolu HTTP.
     - Pokud jste přidali podrobnosti o proxy serveru nebo zakážete proxy server nebo ověřování, kliknutím na **Uložit** spusťte kontrolu připojení znovu.
   - **Instalovat aktualizace**: nástroj automaticky zkontroluje nejnovější aktualizace a nainstaluje je. Nejnovější verzi nástroje můžete nainstalovat také ručně z [tohoto umístění](https://go.microsoft.com/fwlink/?linkid=2134571).
   - **Instalace nástroje microsoft nasazení webu Tool**: nástroj zkontroluje, jestli je na počítači Azure Migrate s Windows, na kterém je spuštěný Nástroj pro zabalení aplikace, nainstalovaný nástroj Microsoft nasazení webu Tool.
   - **Povolit vzdálenou komunikaci prostředí PowerShell**: Tento nástroj vás bude informovat, abyste zajistili, že na aplikačních serverech, na kterých běží aplikace ASP.NET pro kontejner, je povolená Vzdálená komunikace PowerShellu.


## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Klikněte na **Přihlásit** se a přihlaste se k účtu Azure.

1. K ověření pomocí Azure budete potřebovat kód zařízení. Kliknutím na přihlášení se otevře modální okno s kódem zařízení.
2. Kliknutím na **zkopírovat kód & přihlášením** zkopírujte kód zařízení a otevřete výzvu k přihlášení Azure na nové kartě prohlížeče. Pokud se nezobrazí, ujistěte se, že jste v prohlížeči zakázali blokování automaticky otevíraných oken.

    ![Modální zobrazení kódu zařízení](./media/tutorial-containerize-apps-aks/login-modal.png)

3. Na kartě nový vložte kód zařízení a dokončete přihlášení pomocí přihlašovacích údajů k účtu Azure. Po dokončení přihlášení můžete kartu prohlížeče zavřít a vrátit se do webového rozhraní nástroje pro vyplňování kontejneru aplikace.
4. Vyberte **tenanta Azure** , který chcete použít.
5. Zadejte **předplatné Azure** , které chcete použít.

## <a name="discover-aspnet-applications"></a>Zjistit aplikace ASP.NET

Nástroj pro pomoc kontejneru aplikací se vzdáleně připojuje k aplikačním serverům pomocí zadaných přihlašovacích údajů a pokusí se zjistit aplikace ASP.NET hostované na aplikačních serverech.

1. Zadejte **IP adresu nebo plně kvalifikovaný název domény a přihlašovací údaje** serveru, na kterém běží aplikace ASP.NET, která se má použít pro vzdálené připojení k serveru pro zjišťování aplikací.
    - Zadané přihlašovací údaje musí být místního správce (Windows) na aplikačním serveru.
    - U doménových účtů (uživatel musí být správcem na aplikačním serveru), prefixujte uživatelské jméno s názvem domény ve formátu *<doména \ uživatelské_jméno>*.
    - Můžete spustit zjišťování aplikací pro až pět serverů v čase.

2. Kliknutím na tlačítko **ověřit** ověřte, zda je aplikační server dosažitelný z počítače, na kterém je spuštěn nástroj, a zda jsou pověření platná. Po úspěšném ověření se ve sloupci Stav zobrazí stav **namapováno**.  

    ![Snímek obrazovky pro IP adresu serveru a přihlašovací údaje](./media/tutorial-containerize-apps-aks/discovery-credentials.png)

3. Klikněte na **pokračovat** a spusťte zjišťování aplikací na vybraných aplikačních serverech.

4. Po úspěšném dokončení zjišťování aplikací můžete vybrat seznam aplikací, které se kontejnerizace.

    ![Snímek obrazovky s zjištěnou aplikací ASP.NET](./media/tutorial-containerize-apps-aks/discovered-app.png)


4. Pomocí zaškrtávacího políčka vyberte aplikace, které se kontejnerizace.
5. **Zadejte název kontejneru**: zadejte název cílového kontejneru pro každou vybranou aplikaci. Název kontejneru by měl být zadán jako <*Název: značka*>, kde se značka používá pro Image kontejneru. Například můžete zadat název cílového kontejneru jako *AppName: V1*.   

### <a name="parameterize-application-configurations"></a>Parametrizovat konfigurace aplikace
Parametrizace konfigurace je dostupná jako parametr doby nasazení. To vám umožní nakonfigurovat toto nastavení při nasazení aplikace na rozdíl od jejího pevně zakódovaného na určitou hodnotu v imagi kontejneru. Tato možnost je užitečná například pro parametry, jako jsou databázové připojovací řetězce.
1. Klikněte na **Konfigurace aplikace** a zkontrolujte zjištěné konfigurace.
2. Zaškrtnutím políčka můžete parametrizovat zjištěné konfigurace aplikace.
3. Po výběru konfigurací k parametrizovat klikněte na **použít** .

   ![Snímek obrazovky s konfigurací aplikace Parametrizace ASP.NET.](./media/tutorial-containerize-apps-aks/discovered-app-configs.png)

### <a name="externalize-file-system-dependencies"></a>Závislosti systému souborů Externalize

 Můžete přidat další složky, které vaše aplikace používá. Určete, jestli mají být součástí image kontejneru, nebo jestli se mají externě sdílet přes trvalé svazky v Azure File Share. Použití trvalých svazků funguje skvěle pro stavové aplikace, které ukládají stav mimo kontejner, nebo mají jiný statický obsah uložený v systému souborů. [Další informace](https://docs.microsoft.com/azure/aks/concepts-storage)

1. Ve složkách aplikace klikněte na **Upravit** a zkontrolujte zjištěné složky aplikace. Zjištěné složky aplikace byly identifikovány jako povinné artefakty, které vyžaduje aplikace a budou zkopírovány do bitové kopie kontejneru.

2. Klikněte na **Přidat složky** a zadejte cesty ke složkám, které se mají přidat.
3. Chcete-li do stejného svazku přidat více složek, zadejte hodnoty oddělené čárkou ( `,` ).
4. Pokud chcete, aby se složky uložily mimo kontejner na trvalém svazku, vyberte možnost **trvalá jednotka** jako úložiště.
5. Po zkontrolování složek aplikace klikněte na **Uložit** .
   ![Snímek obrazovky s výběrem úložiště pro svazky aplikací](./media/tutorial-containerize-apps-aks/discovered-app-volumes.png)

6. Kliknutím na **pokračovat** přejděte do fáze sestavení image kontejneru.

## <a name="build-container-image"></a>Sestavení image kontejneru


1. **Vyberte Azure Container Registry**: pomocí rozevíracího seznamu vyberte [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) , který se použije k sestavení a uložení imagí kontejneru pro aplikace. Můžete použít existující Azure Container Registry nebo zvolit vytvoření nového pomocí možnosti registru vytvořit novou.

    ![Snímek obrazovky pro výběr aplikace ACR](./media/tutorial-containerize-apps-aks/build-aspnet-app.png)


2. **Přečtěte si souboru Dockerfile**: souboru Dockerfile potřebný k sestavení imagí kontejneru pro každou vybranou aplikaci se vygenerují na začátku kroku sestavení. Klikněte na tlačítko **zkontrolovat** a zkontrolujte souboru Dockerfile. Do souboru Dockerfile můžete také přidat jakékoli nezbytné vlastní nastavení v kroku kontroly a před zahájením procesu sestavení změny uložit.

3. **Spustit proces sestavení**: Vyberte aplikace, pro které chcete sestavit image, a klikněte na **sestavit**. Kliknutím na sestavit se spustí sestavení image kontejneru pro každou aplikaci. Nástroj průběžně udržuje stav buildu a umožní vám přejít k dalšímu kroku po úspěšném dokončení sestavení.

4. **Sledovat stav sestavení**: můžete také sledovat průběh kroku sestavení kliknutím na odkaz **průběh sestavení** ve sloupci Stav. Po aktivaci procesu sestavení tento odkaz trvá několik minut, než bude aktivní.  

5. Po dokončení sestavení klikněte na **pokračovat** a zadejte nastavení nasazení.

    ![Snímek obrazovky s dokončováním sestavení image kontejneru aplikace](./media/tutorial-containerize-apps-aks/build-aspnet-app-completed.png)

## <a name="deploy-the-containerized-app-on-aks"></a>Nasazení kontejnerové aplikace v AKS

Po vytvoření image kontejneru je dalším krokem nasazení aplikace jako kontejneru ve [službě Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/).

1. **Vyberte cluster služby Azure Kubernetes**: Zadejte cluster AKS, do kterého se má aplikace nasadit.

     - Vybraný cluster AKS musí mít fond uzlů Windows.
     - Cluster musí být nakonfigurovaný tak, aby povoloval přijímání imagí z Azure Container Registry, které jste zvolili k uložení imagí.
         - Spuštěním následujícího příkazu v Azure CLI připojte cluster AKS k ACR.
           ``` Azure CLI
           az aks update -n <cluster-name> -g <cluster-resource-group> --attach-acr <acr-name>
           ```  
     - Pokud nemáte cluster AKS nebo chcete vytvořit nový cluster AKS pro nasazení aplikace do nástroje, můžete zvolit vytvoření z nástroje kliknutím na **vytvořit nový cluster AKS**.      
          - Cluster AKS vytvořený pomocí nástroje se vytvoří s fondem uzlů Windows. Cluster se nakonfiguruje tak, aby umožňoval vyžádat si image z Azure Container Registry, které jste vytvořili dříve (Pokud jste zvolili možnost vytvořit novou položku registru).
     - Po výběru clusteru AKS klikněte na **pokračovat** .

2. **Zadejte sdílenou složku Azure**: Pokud jste přidali další složky a vybrali možnost trvalého svazku, zadejte sdílenou složku Azure, kterou má Azure Migrate: Nástroj pro vytvoření kontejneru aplikace během procesu nasazení. Nástroj vytvoří nové adresáře v této sdílené složce Azure pro kopírování složek aplikace, které jsou nakonfigurované pro úložiště trvalých svazků. Po dokončení nasazení aplikace nástroj vyčistí sdílenou složku Azure odstraněním adresářů, které se vytvořily.

     - Pokud nemáte sdílenou složku Azure nebo chcete vytvořit novou sdílenou složku Azure, můžete si ji vytvořit z nástroje kliknutím na **vytvořit nový účet úložiště a sdílení souborů**.  

3. **Konfigurace nasazení aplikace**: po dokončení kroků uvedených v části bude nutné zadat konfiguraci nasazení pro aplikaci. Pro přizpůsobení nasazení aplikace klikněte na tlačítko **Konfigurovat** . V kroku konfigurace můžete zadat následující vlastní nastavení:
     - **Řetězec předpony**: Zadejte předponu řetězce, který se použije v názvu pro všechny prostředky, které jsou vytvořené pro kontejnerové aplikace v clusteru AKS.
     - **Certifikát SSL**: Pokud vaše aplikace vyžaduje vazbu webu https, zadejte soubor PFX, který obsahuje certifikát, který se má použít pro vazbu. Soubor PFX by neměl být chráněný heslem a původní web by neměl mít více vazeb.
     - **Sady replik**: zadejte počet instancí aplikace (lusky), které by měly být spuštěny uvnitř kontejnerů.
     - **Typ nástroje pro vyrovnávání zatížení**: vyberte *externí* , pokud má být kontejnerová aplikace dosažitelná z veřejných sítí.
     - **Konfigurace aplikace**: u všech konfigurací aplikace, které byly parametrizované, zadejte hodnoty, které se mají použít pro aktuální nasazení.
     - **Úložiště**: pro všechny složky aplikací, které byly nakonfigurované pro úložiště trvalého svazku, určete, jestli se má svazek sdílet mezi instancemi aplikace, nebo by se měl inicializovat jednotlivě s každou instancí v kontejneru. Ve výchozím nastavení jsou všechny složky aplikací na trvalých svazcích nakonfigurovány jako sdílené.  
     - Kliknutím na **použít** uložte konfiguraci nasazení.
     - Kliknutím na **pokračovat** nasadíte aplikaci.

    ![Snímek obrazovky s konfigurací aplikace nasazení](./media/tutorial-containerize-apps-aks/deploy-aspnet-app-config.png)

4. **Nasazení aplikace**: po uložení konfigurace nasazení pro aplikaci nástroj vygeneruje YAML nasazení Kubernetes pro aplikaci.
     - Klikněte na **Upravit** a zkontrolujte a přizpůsobte KUBERNETES nasazení YAML pro aplikace.
     - Vyberte aplikaci, kterou chcete nasadit.
     - Kliknutím na **nasadit** spustíte nasazení pro vybrané aplikace.

         ![Snímek obrazovky s konfigurací nasazení aplikace](./media/tutorial-containerize-apps-aks/deploy-aspnet-app-deploy.png)

     - Po nasazení aplikace můžete kliknutím na sloupec *stav nasazení* sledovat prostředky, které byly pro aplikaci nasazeny.

## <a name="download-generated-artifacts"></a>Stáhnout vygenerované artefakty

Všechny artefakty, které se používají k sestavení a nasazení aplikace do AKS, včetně souborů specifikace souboru Dockerfile a Kubernetes YAML, jsou uložené v počítači, na kterém je nástroj spuštěný. Artefakty jsou umístěny v *C:\ProgramData\Microsoft Azure Migrate kontejneru aplikace*.

Pro každý aplikační server se vytvoří jedna složka. Všechny mezilehlé artefakty, které se používají v procesu vytvoření kontejneru, můžete zobrazit a stáhnout tak, že přejdete do této složky. Složka, která odpovídá aplikačnímu serveru, se vyčistí na začátku každého spuštění nástroje pro určitý server.

## <a name="troubleshoot-issues"></a>Řešení potíží

Chcete-li vyřešit všechny problémy s nástrojem, můžete se podívat na soubory protokolu na počítači s Windows, na kterém běží Nástroj pro kontejnery aplikací. Soubory protokolu nástroje jsou umístěné ve složce *C:\ProgramData\Microsoft Azure Migrate App Containerization\Logs* .

## <a name="next-steps"></a>Další kroky

- Uzavření Java Web Apps na Apache Tomcat (na serverech se systémem Linux) a nasaďte je do kontejnerů Linux v AKS. [Další informace](./tutorial-containerize-java-kubernetes.md)
