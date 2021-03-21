---
title: Vytváření pracovních prostorů na portálu
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet, zobrazovat a odstraňovat Azure Machine Learning pracovní prostory v Azure Portal nebo v sadě SDK pro Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: 472bc66c75881d622e8ecfe23031f58db773a919
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518921"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>Vytváření a Správa pracovních prostorů Azure Machine Learning 

V tomto článku vytvoříte, zobrazíte a odstraníte [**Azure Machine Learning pracovní prostory**](concept-workspace.md) pro [Azure Machine Learning](overview-what-is-azure-ml.md)pomocí Azure Portal nebo [sady SDK pro Python](/python/api/overview/azure/ml/) .

Jak se vaše potřeby mění nebo jsou požadavky na automatizaci, můžete také vytvořit a odstranit pracovní prostory [pomocí rozhraní](reference-azure-machine-learning-cli.md)příkazového řádku nebo [pomocí rozšíření vs Code](tutorial-setup-vscode-extension.md).

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes
* Pokud používáte sadu SDK Python, [nainstalujte sadu SDK](/python/api/overview/azure/ml/install).

## <a name="limitations"></a>Omezení

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

Při vytváření pracovního prostoru se ve výchozím nastavení vytvoří taky Azure Container Registry (ACR).  Vzhledem k tomu, že ACR v současné době nepodporuje znaky Unicode v názvech skupin prostředků, použijte skupinu prostředků, která tyto znaky neobsahuje.

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

# <a name="python"></a>[Python](#tab/python)

* **Výchozí specifikace.** Ve výchozím nastavení se automaticky vytvoří závislé prostředky i skupina prostředků. Tento kód vytvoří pracovní prostor s názvem `myworkspace` a skupinu prostředků s názvem `myresourcegroup` v `eastus2` .
    
    ```python
    from azureml.core import Workspace
    
    ws = Workspace.create(name='myworkspace',
                   subscription_id='<azure-subscription-id>',
                   resource_group='myresourcegroup',
                   create_resource_group=True,
                   location='eastus2'
                   )
    ```
    Nastavte `create_resource_group` na hodnotu false, pokud máte existující skupinu prostředků Azure, kterou chcete použít pro pracovní prostor.

* <a name="create-multi-tenant"></a>**Více tenantů.**  Pokud máte více účtů, přidejte ID tenanta Azure Active Directory, který chcete použít.  Vyhledejte ID tenanta z [Azure Portal](https://portal.azure.com) v části **Azure Active Directory, externí identity**.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **[Svrchovaný Cloud](reference-machine-learning-cloud-parity.md)**. Pokud pracujete v rámci svrchovaného cloudu, budete potřebovat další kód pro ověření v Azure.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **Použijte existující prostředky Azure**.  Můžete také vytvořit pracovní prostor, který používá existující prostředky Azure s formátem ID prostředku Azure. V Azure Portal nebo v sadě SDK najdete konkrétní ID prostředků Azure. Tento příklad předpokládá, že skupina prostředků, účet úložiště, Trezor klíčů, App Insights a Registry kontejneru již existují.

   ```python
   import os
   from azureml.core import Workspace
   from azureml.core.authentication import ServicePrincipalAuthentication

   service_principal_password = os.environ.get("AZUREML_PASSWORD")

   service_principal_auth = ServicePrincipalAuthentication(
      tenant_id="<tenant-id>",
      username="<application-id>",
      password=service_principal_password)

                        auth=service_principal_auth,
                             subscription_id='<azure-subscription-id>',
                             resource_group='myresourcegroup',
                             create_resource_group=False,
                             location='eastus2',
                             friendly_name='My workspace',
                             storage_account='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.storage/storageaccounts/mystorageaccount',
                             key_vault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/mykeyvault',
                             app_insights='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.insights/components/myappinsights',
                             container_registry='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.containerregistry/registries/mycontainerregistry',
                             exist_ok=False)
   ```

Další informace najdete v tématu [referenční informace k sadě SDK pracovního prostoru](/python/api/azureml-core/azureml.core.workspace.workspace).

Pokud máte problémy s přístupem k předplatnému, přečtěte si téma [nastavení ověřování pro Azure Machine Learning prostředky a pracovní postupy a](how-to-setup-authentication.md)také [ověřování v](https://aka.ms/aml-notebook-auth) poznámkovém bloku Azure Machine Learning.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí přihlašovacích údajů předplatného Azure. 

1. V levém horním rohu Azure Portal vyberte **+ vytvořit prostředek**.

      ![Vytvoření nového prostředku](./media/how-to-manage-workspace/create-workspace.gif)

1. K vyhledání **Machine Learning** použijte panel hledání.

1. Vyberte **Machine Learning**.

1. V podokně **Machine Learning** vyberte **vytvořit** a začněte.

1. Zadáním následujících informací nakonfigurujte nový pracovní prostor:

   Pole|Description 
   ---|---
   Název pracovního prostoru |Zadejte jedinečný název, který identifikuje váš pracovní prostor. V tomto příkladu používáme **docs-WS**. Názvy musí být v rámci skupiny prostředků jedinečné. Použijte název, který se dá snadno vyvolat a odlišit z pracovních prostorů vytvořených jinými uživateli. V názvu pracovního prostoru se nerozlišují malá a velká písmena.
   Předplatné |Vyberte předplatné Azure, které chcete použít.
   Skupina prostředků | Použijte stávající skupinu prostředků, kterou máte v předplatném, nebo zadejte název a vytvořte novou skupinu prostředků. Skupina prostředků obsahuje související prostředky pro řešení Azure. V tomto příkladu používáme **docs-AML**. Chcete-li použít existující skupinu prostředků, potřebujete roli *Přispěvatel* nebo *vlastník* .  Další informace o přístupu najdete v tématu [Správa přístupu k pracovnímu prostoru Azure Machine Learning](how-to-assign-roles.md).
   Oblast | Vyberte oblast Azure, která je nejblíže vašim uživatelům a datovým prostředkům k vytvoření pracovního prostoru.
   | Účet úložiště | Výchozí účet úložiště pro pracovní prostor. Ve výchozím nastavení se vytvoří nový. |
   | Key Vault | Azure Key Vault používané pracovním prostorem. Ve výchozím nastavení se vytvoří nový. |
   | Application Insights | Instance Application Insights pro pracovní prostor. Ve výchozím nastavení se vytvoří nový. |
   | Container Registry | Azure Container Registry pracovního prostoru Ve výchozím nastavení se pro tento pracovní _prostor poprvé_ nevytvoří nový. Místo toho se vytvoří, až ho budete potřebovat při vytváření image Docker během školení nebo nasazení. |

   :::image type="content" source="media/how-to-manage-workspace/create-workspace-form.png" alt-text="Nakonfigurujte svůj pracovní prostor.":::

1. Po dokončení konfigurace pracovního prostoru vyberte **zkontrolovat + vytvořit**. Volitelně můžete pomocí oddílů [sítě](#networking) a [Upřesnit](#advanced) nakonfigurovat další nastavení pracovního prostoru.

1. Zkontrolujte nastavení a proveďte další změny nebo opravy. Až budete s nastavením spokojeni, vyberte **vytvořit**.

   > [!Warning] 
   > Vytvoření pracovního prostoru v cloudu může trvat několik minut.

   Po dokončení procesu se zobrazí zpráva o úspěšném nasazení. 
 
 1. Pokud chcete zobrazit nový pracovní prostor, vyberte **Přejít k prostředku**.
 
---



### <a name="networking"></a>Sítě  

> [!IMPORTANT]  
> Další informace o používání privátního koncového bodu a virtuální sítě s vaším pracovním prostorem najdete v tématu věnovaném [izolaci sítě a ochraně osobních údajů](how-to-network-security-overview.md).


# <a name="python"></a>[Python](#tab/python)

Sada Azure Machine Learning Python SDK poskytuje třídu [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) , která se dá použít s [pracovním prostorem. Create ()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) k vytvoření pracovního prostoru s privátním koncovým bodem. Tato třída vyžaduje existující virtuální síť.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Výchozí konfigurací sítě je použití __veřejného koncového bodu__, který je přístupný na veřejném Internetu. Pokud chcete omezit přístup k vašemu pracovnímu prostoru na Virtual Network Azure, kterou jste vytvořili, můžete místo toho vybrat jako __metodu připojení__ __privátní koncový bod__ (Preview) a pak pomocí __+ Přidat__ nakonfigurovat koncový bod.   

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="Výběr privátního koncového bodu":::  

1. Ve formuláři __Vytvoření privátního koncového bodu__ nastavte umístění, název a virtuální síť, které se mají použít. Pokud chcete použít koncový bod se zónou Privátní DNS, vyberte možnost __integrovat s privátní zónou DNS__ a vyberte zónu pomocí pole __privátní DNS zóna__ . Vyberte __OK__ a vytvořte koncový bod.   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="Vytvoření privátního koncového bodu":::   

1. Po dokončení konfigurace sítě můžete vybrat možnost __zkontrolovat + vytvořit__ nebo přejít k volitelné __Rozšířené__ konfiguraci.

---

> [!IMPORTANT]  
> Používání privátního koncového bodu s Azure Machine Learning pracovním prostorem je v současnosti ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.     
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="multiple-workspaces-with-private-endpoint"></a>Několik pracovních prostorů s privátním koncovým bodem

Při vytváření privátního koncového bodu se vytvoří nová zóna Privátní DNS s názvem __privatelink.API.AzureML.MS__ . Obsahuje odkaz na virtuální síť. Pokud vytvoříte více pracovních prostorů s privátními koncovými body ve stejné skupině prostředků, může být do zóny DNS přidána pouze virtuální síť pro první privátní koncový bod. Chcete-li přidat položky pro virtuální sítě používané dalšími koncovými body nebo soukromými koncovými body, použijte následující postup:

1. V [Azure Portal](https://portal.azure.com)vyberte skupinu prostředků, která obsahuje pracovní prostor. Pak vyberte prostředek zóny Privátní DNS s názvem __privatelink.API.AzureML.MS__
2. V __Nastavení__ vyberte __odkazy virtuální sítě__.
3. Vyberte __Přidat__. Na stránce __Přidat virtuální síť__ zadejte jedinečný __název odkazu__ a potom vyberte __virtuální síť__ , kterou chcete přidat. Kliknutím na __tlačítko OK__ přidejte síťové propojení.

Další informace najdete v tématu [Konfigurace DNS privátního koncového bodu Azure](../private-link/private-endpoint-dns.md).

### <a name="vulnerability-scanning"></a>Kontrola ohrožení zabezpečení

Azure Security Center zajišťuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami napříč hybridními cloudovými úlohami. Měli byste Azure Security Center, abyste mohli kontrolovat prostředky a dodržovat doporučení. Další informace najdete v tématu  [Azure Container Registry prověřování imagí Security Center](../security-center/defender-for-container-registries-introduction.md) a [Azure Kubernetes Services integration with Security Center](../security-center/defender-for-kubernetes-introduction.md).

### <a name="advanced"></a>Pokročilý

Ve výchozím nastavení se metadata pro pracovní prostor ukládají do Azure Cosmos DB instance, kterou společnost Microsoft udržuje. Tato data se šifrují pomocí klíčů spravovaných Microsoftem.

Pokud chcete omezit data, která Microsoft shromažďuje v pracovním prostoru, vyberte __vysoký pracovní prostor pro obchodní dopad__ na portálu nebo nastavte `hbi_workspace=true ` v Pythonu. Další informace o tomto nastavení najdete v části [šifrování v klidovém umístění](concept-data-encryption.md#encryption-at-rest).

> [!IMPORTANT]  
> Výběr vysokého dopadu na firmu se dá udělat jenom při vytváření pracovního prostoru. Po vytvoření pracovního prostoru toto nastavení nemůžete změnit.   

#### <a name="use-your-own-key"></a>Použít vlastní klíč

Můžete zadat vlastní klíč pro šifrování dat. Tím se vytvoří instance Azure Cosmos DB, která ukládá metadata v předplatném Azure.

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

K poskytnutí vlastního klíče použijte následující postup:

> [!IMPORTANT]  
> Před provedením tohoto postupu je třeba nejprve provést následující akce:   
>
> 1. Autorizaci __aplikace Machine Learning__ (v části Správa identit a přístupu) s oprávněními přispěvatele v předplatném.  
> 1. Postupujte podle kroků v části [konfigurace klíčů spravovaných zákazníkem](../cosmos-db/how-to-setup-cmk.md) na:
>     * Registrace poskytovatele Azure Cosmos DB
>     * Vytvoření a konfigurace Azure Key Vault
>     * Vygenerovat klíč
>   
>     Nemusíte vytvářet instanci Azure Cosmos DB ručně, ta se vytvoří během vytváření pracovního prostoru. Tato instance Azure Cosmos DB se vytvoří v samostatné skupině prostředků s použitím názvu založeného na tomto vzoru: `<your-workspace-resource-name>_<GUID>` .   
>   
> Po vytvoření pracovního prostoru toto nastavení nemůžete změnit. Pokud odstraníte Azure Cosmos DB, kterou používá váš pracovní prostor, musíte také odstranit pracovní prostor, který ho používá.

# <a name="python"></a>[Python](#tab/python)

Pomocí `cmk_keyvault` a `resource_cmk_uri` můžete zadat klíč spravovaný zákazníkem.

```python
from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
               subscription_id='<azure-subscription-id>',
               resource_group='myresourcegroup',
               create_resource_group=True,
               location='eastus2'
               cmk_keyvault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/<keyvault-name>', 
               resource_cmk_uri='<key-identifier>'
               )

```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Vyberte __klíče spravované zákazníkem__ a pak vyberte __možnost kliknutím vyberte klíč__.

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="Klíče spravované zákazníkem":::

1. Na Azure Key Vault ve formuláři __Vyberte klíč__ vyberte existující Azure Key Vault, klíč, který obsahuje, a verzi klíče. Tento klíč slouží k šifrování dat uložených v Azure Cosmos DB. Nakonec k použití tohoto klíče použijte tlačítko __Vybrat__ .

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="Vyberte klíč.":::

---

### <a name="download-a-configuration-file"></a>Stažení konfiguračního souboru

Pokud budete vytvářet [výpočetní instanci](tutorial-1st-experiment-sdk-setup.md#azure), tento krok přeskočte.  Instance COMPUTE již pro vás vytvořila kopii tohoto souboru.

# <a name="python"></a>[Python](#tab/python)

Pokud plánujete použít kód v místním prostředí, které odkazuje na tento pracovní prostor ( `ws` ), zapište konfigurační soubor:

```python
ws.write_config()
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pokud máte v úmyslu používat v místním prostředí kód, který odkazuje na tento pracovní prostor, vyberte  **stáhnout config.jsv** části **Přehled** v pracovním prostoru.  

   ![Stáhnout config.js](./media/how-to-manage-workspace/configure.png)

---

Soubor umístěte do struktury adresáře pomocí skriptů Pythonu nebo poznámkových bloků Jupyter. Může být ve stejném adresáři, v podadresáři s názvem *. AzureML* nebo v nadřazeném adresáři. Při vytváření výpočetní instance se tento soubor přidá do správného adresáře na virtuálním počítači za vás.

## <a name="connect-to-a-workspace"></a>Připojení k pracovnímu prostoru

V kódu Pythonu vytvoříte objekt pracovního prostoru pro připojení k vašemu pracovnímu prostoru.  Tento kód přečte obsah konfiguračního souboru a zjistí váš pracovní prostor.  Zobrazí se výzva, abyste se přihlásili, pokud ještě nejste ověřeni.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

* <a name="connect-multi-tenant"></a>**Více tenantů.**  Pokud máte více účtů, přidejte ID tenanta Azure Active Directory, který chcete použít.  Vyhledejte ID tenanta z [Azure Portal](https://portal.azure.com) v části **Azure Active Directory, externí identity**.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.from_config(auth=interactive_auth)
    ```

* **[Svrchovaný Cloud](reference-machine-learning-cloud-parity.md)**. Pokud pracujete v rámci svrchovaného cloudu, budete potřebovat další kód pro ověření v Azure.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.from_config(auth=interactive_auth)
    ```
    
Pokud máte problémy s přístupem k předplatnému, přečtěte si téma [nastavení ověřování pro Azure Machine Learning prostředky a pracovní postupy a](how-to-setup-authentication.md)také [ověřování v](https://aka.ms/aml-notebook-auth) poznámkovém bloku Azure Machine Learning.

## <a name="find-a-workspace"></a><a name="view"></a>Najít pracovní prostor

Podívejte se na seznam všech pracovních prostorů, které můžete použít.

# <a name="python"></a>[Python](#tab/python)

Vaše předplatná najdete na [stránce Předplatná v Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).  Zkopírujte ID a použijte ho v následujícím kódu, abyste viděli všechny pracovní prostory, které jsou pro toto předplatné k dispozici.

```python
from azureml.core import Workspace

Workspace.list('<subscription-id>')
```

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Do pole nejvyšší hledání zadejte **Machine Learning**.  

1. Vyberte **Machine Learning**.

   ![Vyhledat Azure Machine Learning pracovní prostor](./media/how-to-manage-workspace/find-workspaces.png)

1. Prohlédněte si seznam pracovních prostorů, které se našly. Můžete filtrovat podle předplatného, skupin prostředků a umístění.  

1. Vyberte pracovní prostor, ve kterém chcete zobrazit jeho vlastnosti.

---


## <a name="delete-a-workspace"></a>Odstranění pracovního prostoru

Pokud už pracovní prostor nepotřebujete, odstraňte ho.  

# <a name="python"></a>[Python](#tab/python)

Odstranit pracovní prostor `ws` :

```python
ws.delete(delete_dependent_resources=False, no_wait=False)
```

Výchozí akcí není odstranit prostředky přidružené k pracovnímu prostoru, tj. registr kontejnerů, účtu úložiště, trezoru klíčů a Application Insights.  Chcete `delete_dependent_resources` -li odstranit i tyto prostředky, nastavte na hodnotu true.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

V [Azure Portal](https://portal.azure.com/)v horní části pracovního prostoru, který chcete odstranit, vyberte **Odstranit**  .

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Odstranit pracovní prostor":::

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Poradce při potížích

* **Podporované prohlížeče v Azure Machine Learning Studiu**: Doporučujeme, abyste používali nejaktuálnější prohlížeč, který je kompatibilní s vaším operačním systémem. Podporovány jsou následující prohlížeče:
  * Microsoft Edge (nová Microsoft Edge, nejnovější verze Ne Microsoft Edge starší verze)
  * Safari (nejnovější verze, jen Mac)
  * Chrome (nejnovější verze)
  * Firefox (nejnovější verze)

* **Azure Portal**: 
  * Pokud přejdete přímo k pracovnímu prostoru z odkazu pro sdílení ze sady SDK nebo Azure Portal, nemůžete zobrazit standardní stránku s **přehledem** , která obsahuje informace o předplatném v rozšíření. V tomto scénáři se také nedá přepnout na jiný pracovní prostor. Pokud chcete zobrazit jiný pracovní prostor, přejít přímo na [Azure Machine Learning Studio](https://ml.azure.com) a vyhledejte název pracovního prostoru.
  * Všechny prostředky (datové sady, experimenty, výpočty atd.) jsou k dispozici pouze v [Azure Machine Learning Studiu](https://ml.azure.com). *Nejsou* k dispozici z Azure Portal.

### <a name="resource-provider-errors"></a>Chyby poskytovatele prostředků

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Přesun pracovního prostoru

> [!WARNING]
> Přesunutím pracovního prostoru Azure Machine Learning do jiného předplatného nebo přesunutím vlastnícího předplatného na nového tenanta se nepodporuje. V takovém případě může dojít k chybám.

### <a name="deleting-the-azure-container-registry"></a>Odstranění Azure Container Registry

Azure Machine Learning pracovní prostor používá pro některé operace Azure Container Registry (ACR). Automaticky vytvoří instanci ACR, když ji poprvé potřebuje.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="examples"></a>Příklady

Příklady vytvoření pracovního prostoru:
* Použití Azure Portal k [Vytvoření pracovního prostoru a instance COMPUTE](tutorial-1st-experiment-sdk-setup.md)
* Použití sady Python SDK k [Vytvoření pracovního prostoru ve vlastním prostředí](tutorial-1st-experiment-sdk-setup-local.md)

## <a name="next-steps"></a>Další kroky

Jakmile máte pracovní prostor, Naučte se, jak se naučit [a nasazovat model](tutorial-train-models-with-aml.md).