---
title: "Sestavení projektu v Azure pomocí volaných a instancí kontejnerů Azure"
description: "Naučte se používat modul plug-in Azure kontejneru Agent pro volaných pro vytvoření projektu v Azure s instancemi Azure kontejneru"
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: fc3ad4b68e29e9bd5666bb115306b452d074f682
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="build-a-project-in-azure-using-jenkins-and-azure-container-instances"></a>Sestavení projektu v Azure pomocí volaných a instancí kontejnerů Azure

Azure instancí kontejnerů umožňuje snadno zprovoznění a spuštěna bez nutnosti zřizovat virtuální počítače nebo přijmout vyšší úrovně služby. Azure instancí kontejnerů poskytuje fakturace za sekundu na základě kapacity, které potřebujete; Díky tomu atraktivní možnost pro přechodný úlohy, například při provádění sestavení.

Získáte informace o těchto tématech:
> [!div class="checklist"]
> * Instalace a konfigurace serveru volaných na Azure
> * Instalace a konfigurace modulu plug-in Azure Container agentů pro volaných
> * Použít k vytvoření instancí kontejnerů Azure [pružiny PetClinic ukázkové aplikace](https://github.com/spring-projects/spring-petclinic)

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure** – Pokud chcete další informace o možnostech nákupu Azure najdete v tématu [jak koupit Azure](https://azure.microsoft.com/pricing/purchase-options/) nebo [bezplatnou zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

- **Azure CLI 2.0 nebo prostředí cloudu Azure** -nainstalovat některou z následujících produktů, do kterého chcete zadat příkazy Azure:

    - [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) -vám umožní spustit příkazy Azure pro příkaz nebo okno terminálu.
    - [Prostředí Azure Cloud](/azure/cloud-shell/quickstart.md) – založené na prohlížeči prostředí shell prostředí. Cloudové prostředí umožňuje přístup k založené na prohlížeči příkazového řádku prostředí, vytvořené s nástroji úlohy správy Azure v paměti.

## <a name="install-a-jenkins-server-on-azure-using-the-jenkins-marketplace-image"></a>Instalace serveru volaných v Azure pomocí bitové kopie volaných Marketplace

Volaných podporuje model kde volaných serveru delegáti spolupracovat na jeden nebo více agentů umožňuje jednotnou instalaci volaných hostovat velký počet projektů nebo poskytovat potřebné pro různá prostředí sestavení nebo testy. Postup v této části Průvodce vás provede instalací a konfigurací volaných server na platformě Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-the-jenkins-server-running-on-azure"></a>Připojení k serveru volaných spuštěné v Azure

Po instalaci volaných v Azure, budete muset připojit k volaných. Následující postup vás provede procesem nastavení připojení SSH pro virtuální počítač volaných spuštěné v Azure. 

[!INCLUDE [jenkins-connect-to-jenkins-server-running-on-azure](../../includes/jenkins-connect-to-jenkins-server-running-on-azure.md)]

## <a name="update-jenkins-dns"></a>Update Jenkins DNS

Volaných musí znát vlastní adresu URL, když se vytváří odkazy, které odkazují na sebe sama. Například adresa URL se musí použít při volaných odešle e-mailů obsahujících přímé odkazy na sestavení výsledky. 

Tato část vás provede procesem nastavování volaných adresy URL.

1. V prohlížeči přejděte na řídicí panel volaných v `http://localhost:8080`.

1. Vyberte **spravovat volaných**.

    ![Spravovat volaných možnosti v řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Vyberte **konfiguraci systému**.

    ![Správa modulů plug-in možnost volaných v řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. V části **volaných umístění**, zadejte adresu URL serveru volaných.

1. Vyberte **Uložit**.

## <a name="update-jenkins-to-allow-java-network-launch-protocol-jnlp"></a>Aktualizace volaných umožňující Java síťové spuštění protokolu (JNLP)

Agent volaných připojení k serveru volaných prostřednictvím Java síťové spuštění protokolu (JNLP). Tato část vysvětluje, jak zadejte port pro použitých při komunikaci se serverem volaných JNLP agentů.

1. Na řídicím panelu volaných vyberte **spravovat volaných**.

    ![Spravovat volaných možnosti v řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Vyberte **konfigurace globálního zabezpečení**.

    ![Konfigurace globálního zabezpečení v řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-global-security.png)

1. V části **agenti**, vyberte **pevné**a zadejte port. Snímek obrazovky ukazuje hodnotu portu 12345 jako příklad. Musíte zadat port, který dává smysl pro vaše prostředí.

    ![Aktualizovat nastavení volaných globální zabezpečení umožňující JNLP](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-set-jnlp.png)

1. Vyberte **Uložit**.

1. Pomocí Azure CLI 2.0 nebo cloudové prostředí, zadejte následující příkaz pro vytvoření příchozího pravidla pro vaší skupinu zabezpečení sítě volaných:

    ```azurecli
    az network nsg rule create  \
    --resource-group JenkinsResourceGroup \
    --nsg-name JenkinsNSG  \
    --name allow-https \
    --description "Allow access to port 12345 for HTTPS" \
    --access Allow \
    --protocol Tcp  \
    --direction Inbound \
    --priority 102 \
    --source-address-prefix "*"  \
    --source-port-range "*"  \
    --destination-address-prefix "*" \
    --destination-port-range "12345"
    ```

## <a name="create-and-add-an-azure-service-principal-to-the-jenkins-credentials"></a>Vytvořit a přidat objektu zabezpečení služby Azure na volaných pověření

Budete potřebovat objektu zabezpečení služby Azure k nasazení do Azure. Následující postup vás provede procesem vytvoření hlavní název služby (Pokud jste ještě nemáte) a aktualizace volaných s instanční objekt.

1. Pokud již máte hlavní název služby (a znáte jeho ID předplatného, klienty, appId a heslo), můžete tento krok přeskočit. Pokud potřebujete vytvořit objekt zabezpečení, podívejte se na článek [vytvořit objekt služby Azure pomocí Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Při vytváření objektu zabezpečení, poznamenejte si hodnoty pro ID předplatného, klienty, appId a heslo.

1. Vyberte **pověření**.

    ![Spravovat možnost přihlašovacích údajů na řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials.png)

1. V části **pověření**, vyberte **systému**.

    ![Spravovat možnost pověření systému v řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-system.png)

1. Vyberte **globální přihlašovací údaje (neomezený)**.

    ![Spravovat globální systému možnost přihlašovacích údajů na řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-global.png)

1. Vyberte **přidání některé přihlašovací údaje**.

    ![Přidat přihlašovací údaje v řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-adding-credentials.png)

1. V **druhu** rozevíracího seznamu vyberte **Microsoft Azure Service Principal** způsobí stránky pro zobrazení konkrétních polí při přidávání objektu služby. Potom zadejte požadované hodnoty následujícím způsobem:

    - **Obor** -vyberte možnost pro **globální (volaných uzly, položky, všechny podřízené položky, atd.)** .
    - **ID předplatného** -použít ID předplatného Azure, který jste zadali při spuštění `az account set`.
    - **ID klienta** -použití `appId` hodnota vrácená z `az ad sp create-for-rbac`.
    - **Tajný klíč klienta** -použití `password` hodnota vrácená z `az ad sp create-for-rbac`.
    - **ID klienta** -použití `tenant` hodnota vrácená z `az ad sp create-for-rbac`.
    - **Prostředí Azure** – vyberte `Azure`.
    - **ID** -zadejte `myTestSp`. Tato hodnota se používá později v tomto kurzu.
    - **Popis** (volitelné) zadejte hodnotu popis pro tento objekt zabezpečení.

    ![Zadejte nový hlavní vlastnosti služby v řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-principal-properties.png)

1. Po definování objekt informace, které jste zadali, můžete volitelně vybrat **ověřte instanční objekt** zajistit, že všechno funguje správně. Pokud vaše instanční objekt je správně definován, se zobrazí zpráva s oznámením, "úspěšně ověřila objekt služby Microsoft Azure." níže **popis** pole.

1. Po dokončení vyberte **OK** přidat objekt do volaných. Řídicí panel volaných zobrazí nově přidaný objekt zabezpečení v **globální pověření** stránky.

## <a name="create-an-azure-resource-group-for-your-azure-container-instances"></a>Vytvoření skupiny prostředků Azure pro vaše instance kontejner Azure

Azure instancí kontejnerů musí být umístěny ve skupině prostředků Azure. Skupinu prostředků Azure je kontejner, který obsahuje související prostředky pro řešení s Azure.

Pomocí Azure CLI 2.0 nebo cloudové prostředí, zadejte následující příkaz pro vytvoření skupinu prostředků s názvem `JenkinsAciResourceGroup` v umístění `eastus`:

```azurecli
az group create --name JenkinsAciResourceGroup --location eastus
```

Po dokončení `az group create` příkaz zobrazí výsledky podobně jako v následujícím příkladu:

```JSON
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/JenkinsAciResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "JenkinsAciResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="install-the-azure-container-agents-plugin-for-jenkins"></a>Instalace modulu plug-in Azure Container agentů pro volaných

Pokud jste již nainstalovali modul plug-in Azure Container agentů, můžete tuto část přeskočit.

Jakmile máte vytvořenou pro agenta volaných skupinu prostředků Azure, následující kroky popisují postup instalace modulu plug-in Azure Container agentů:

1. Na řídicím panelu volaných vyberte **spravovat volaných**.

    ![Spravovat volaných možnosti v řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Vyberte **Správa modulů plug-in**.

    ![Správa modulů plug-in možnost volaných v řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-plugins.png)

1. Vyberte **k dispozici**.

    ![Zobrazení k dispozici možnost modulů plug-in volaných v řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-view-available-plugins.png)

1. Zadejte `Azure Container Agents` do **filtru** textové pole. (V seznamu filtrů jako zadejte text.)

    ![Filtrovat dostupné moduly plug-in volaných v řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-filter-available-plugins.png)

1. Zaškrtněte políčko vedle položky **Azure Container agentů** modul plug-in a jednu z možností instalace. Pro účely tuto ukázku, vyberete **nainstalovat bez restartování** možnost.

    ![Instalace modulů plug-in Azure Container agentů na řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin.png)

1.  Až vyberete možnost nainstalovat požadované plugin(s), volaných řídicího panelu zobrazí na stránce s podrobnostmi o stavu co instalujete.

    ![Instalace modulů plug-in Azure Container agentů na řídicím panelu volaných stav instalace](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin-confirmation.png)

    Chcete-li vrátit na hlavní stránku volaných řídicí panel, vyberte **přejděte zpět na hlavní stránku**.

## <a name="configure-the-azure-container-agents-plugin"></a>Konfigurace modulu plug-in Azure Container agentů

Po instalaci modulu plug-in Azure Container agentů v této části vás provede konfiguraci v rámci volaných řídicího panelu.

1. Na řídicím panelu volaných vyberte **spravovat volaných**.

    ![Spravovat volaných možnosti v řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Vyberte **konfiguraci systému**.

    ![Správa modulů plug-in možnost volaných v řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. Vyhledejte **cloudu** část v dolní části stránky a z **přidat nové cloudové** rozevíracího seznamu vyberte **instanci Azure Container**.

    ![Přidat nového poskytovatele cloudu na řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-cloud-provider.png)

1. V **Instance kontejner Azure** části, zadejte následující hodnoty:

    - **Název cloudu** (volitelné jako výchozí hodnota je na automaticky generovaný název.) Zadejte název pro tuto instanci. 
    - **Přihlašovací údaje Azure** – vyberte na šipku rozevíracího seznamu a pak vyberte `myTestSp` položku, která identifikuje objekt zabezpečení služby Azure jste vytvořili dříve.
    - **Skupina prostředků** – vyberte na šipku rozevíracího seznamu a pak vyberte `JenkinsAciResourceGroup` položku, která identifikuje skupinu prostředků Azure kontejneru instanci jste vytvořili dříve.

    ![Definování vlastnosti Instance kontejner Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-properties.png)

1. Vyberte **přidat šablonu kontejneru** šipku rozevíracího seznamu a pak vyberte **Aci kontejneru šablony**.

1. V **Aci kontejneru šablony** části, zadejte následující hodnoty:

    - **Název** -zadejte `ACI-container`.
    - **Popisky** -zadejte `ACI-container`.
    - **Obrázek docker** -zadejte `cloudbees/jnlp-slave-with-java-build-tools`

    ![Definování vlastnosti bitové kopie Instance kontejner Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-image-properties.png)

1. Vyberte **rozšířené**.

1. Vyberte **uchování strategie** šipku rozevíracího seznamu a vyberte **kontejneru nečinnosti uchování strategie**. Výběrem této možnosti volaných udržuje agenta, dokud žádné nové úlohy se spustí v agentovi a uplynutí zadané doby nečinnosti.

    ![Definování strategie uchování Instance kontejner Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-retention-strategy.png)

1. Vyberte **Uložit**.

## <a name="create-the-spring-petclinic-application-job-in-jenkins"></a>Vytvoření úlohy pružiny PetClinic aplikace ve volaných

Následující kroky vás provedou vytváření úlohy volaných - jako volný styl projekt – k vytvoření aplikace PetClinic pružiny.

1. Na řídicím panelu volaných vyberte **novou položku**.

    ![Nové položky nabídky na řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-item.png)

1. Zadejte `myPetClinicProject` pro název položky a vyberte **volný styl projektu**.

    ![Nový projekt volný styl v řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project.png)

1. Vyberte **OK**.

1. Vyberte **Obecné** a zadejte následující hodnoty:

    - **Omezit, kde je možné spustit projekt** – vyberte tuto možnost.
    - **Označení výraz** -zadejte `ACI-container`. Při ukončení pole, zobrazí se zpráva, potvrzení, že popisek obsloužených konfigurace cloudu vytvořili v předchozím kroku.

    ![Obecná nastavení pro nový projekt volný styl v řídicím panelu volaných](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project-general.png)

1. Vyberte **správu zdrojového kódu** a zadejte následující hodnoty:

    - **Zdroj kódu správu** – vyberte **Git**.
    - **Adresu URL úložiště** -zadejte následující adresu URL pro úložiště pružiny PetClinic ukázkové aplikace GitHub: `https://github.com/spring-projects/spring-petclinic.git`.

1. Vyberte **sestavení** kartě a provádět následující úlohy:

    - Vyberte **přidat krok sestavení** šipku rozevíracího seznamu a vyberte **vyvolání nejvyšší úrovně cíle Maven**.

    - Pro **cíle**, zadejte `package`.

1. Vyberte **Uložit** Uložit definici nové projektu.

## <a name="build-the-spring-petclinic-application-job-in-jenkins"></a>Sestavení projektu aplikace PetClinic pružiny ve volaných

Je čas k sestavení projektu! Tato část vysvětluje postup sestavení projektu, na řídicím panelu volaných.

1. Na řídicím panelu volaných vyberte `myPetClinicProject`.

    ![Vyberte projekt, který má na řídicím panelu volaných sestavení.](./media/azure-container-agents-plugin-run-container-as-an-agent/select-project-to-build.png)

1. Vyberte **sestavení teď**. 

    ![Sestavení projektu na řídicím panelu volaných.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-project.png)

1. Když spustíte sestavení v volaných, je zařadit do fronty sestavení. V případě kontejner agenta Azure nelze sestavení spustit, dokud nebude Agent kontejner Azure spustit a uvést do režimu online. Do té doby se zobrazí zprávu s upozorněním, název agenta a fakt, že sestavení čeká na vyřízení. (Tento proces trvat asi pět minut, ale je potřeba, pouze při prvním použití agenta pro sestavení. Následující sestavení jsou mnohem rychlejší, protože agent je v tomto okamžiku online).

    ![Sestavení je zařadit do fronty, dokud nebude agent je vytvořena a uvést do režimu online.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-pending.png)

    Jakmile se spustí sestavení, indikátor průběhu pólu barber naznačuje, že je spuštěn sestavení:

    ![Sestavení je spuštěna, jakmile se zobrazí indikátor průběhu pólu barber.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-running.png)

1. Pokud indikátor průběhu pólu barber zmizí, vyberte na šipku vedle číslo sestavení. V místní nabídce vyberte **konzole výstup**.

    ![Klikněte na položku nabídky protokolu Console zobrazíte informace o sestavení.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-menu.png)

1. Informace protokolu konzole vygenerované z procesu sestavení. Chcete-li zobrazit všechny informace o sestavení (včetně informací o sestavení provádí vzdáleně na Azure agent jste vytvořili), vyberte **úplné protokolu**.

    ![Klikněte na odkaz úplnou protokolu chcete zobrazit podrobnější informace o sestavení.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log.png)

    Plný protokol zobrazí podrobnější informace o sestavení:

    ![Plný protokol zobrazí podrobnější informace o sestavení.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-full.png)
    
1. Chcete-li zobrazit dispozice sestavení, přejděte do dolní protokolu.

    ![V dolní části protokolu sestavení zobrazí dispozice sestavení.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-disposition.png)

## <a name="clean-up-azure-resources"></a>Vyčištění prostředků Azure

V tomto kurzu jste vytvořili prostředky obsažené v rámci dvou skupin prostředků Azure: 
    - `JenkinsResourceGroup` -Obsahuje prostředky Azure pro volaných server.
    - `JenkinsAciResourceGroup` -Obsahuje prostředky Azure pro volaných agenta.
    
Pokud již nepotřebujete používat kterýkoli z prostředků ve skupině prostředků Azure, můžete odstranit skupinu prostředků pomocí `az group delete` příkaz takto (nahrazení &lt;resourceGroup > zástupný symbol název skupiny prostředků, které chcete Odstraňte):

```azurecli
az group delete -n <resourceGroup>
```

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Přejděte volaných na centrum Azure zobrazíte nejnovější články a ukázky](https://docs.microsoft.com/azure/jenkins/)