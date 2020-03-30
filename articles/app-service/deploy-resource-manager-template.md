---
title: Nasazení aplikací pomocí šablon
description: Najděte pokyny k vytváření šablon Azure Resource Manager pro zřizování a nasazování aplikací služby App Service.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: dfdfa9f69e00aa644c21fc96cb70e9fa460ca0c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77211699"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Pokyny k nasazení webových aplikací pomocí šablon Azure Resource Manager

Tento článek obsahuje doporučení pro vytváření šablon Azure Resource Manager pro nasazení řešení služby Azure App Service. Tato doporučení vám mohou pomoci vyhnout se běžným problémům.

## <a name="define-dependencies"></a>Definovat závislosti

Definování závislostí pro webové aplikace vyžaduje pochopení toho, jak prostředky v rámci webové aplikace interagují. Pokud zadáte závislosti v nesprávném pořadí, může způsobit chyby nasazení nebo vytvořit spor, který zastaví nasazení.

> [!WARNING]
> Pokud do šablony zahrnete rozšíření webu MSDeploy, musíte nastavit všechny prostředky konfigurace jako závislé na prostředku MSDeploy. Změny konfigurace způsobí restartování lokality asynchronně. Tím, že prostředky konfigurace závislé na MSDeploy, zajistíte, že MSDeploy dokončí před restartováním lokality. Bez těchto závislostí může být lokalita restartována během procesu nasazení msdeploy. Ukázkovou šablonu najdete v [tématu WordPress Template with Web Deploy Dependency](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

Následující obrázek znázorňuje pořadí závislostí pro různé prostředky služby App Service:

![Závislosti webových aplikací](media/web-sites-rm-template-guidance/web-dependencies.png)

Prostředky nasadíte v následujícím pořadí:

**Vrstva 1**
* Plán služby App Service.
* Všechny ostatní související prostředky, jako jsou databáze nebo účty úložiště.

**Vrstva 2**
* Webová aplikace – závisí na plánu služby App Service.
* Instance Azure Application Insights, která cílí na serverovou farmu – závisí na plánu služby App Service.

**Úroveň 3**
* Ovládací prvek zdroje – závisí na webové aplikaci.
* Rozšíření webu MSDeploy – závisí na webové aplikaci.
* Instance Azure Application Insights, která cílí na webovou aplikaci– závisí na webové aplikaci.

**Úroveň 4**
* Certifikát služby App Service – závisí na správě zdrojového kódu nebo MSDeploy, pokud je k dispozici. V opačném případě záleží na webové aplikaci.
* Nastavení konfigurace (připojovací řetězce, hodnoty web.config, nastavení aplikace)-- závisí na směřování zdrojového kódu nebo MSDeploy, pokud je k dispozici. V opačném případě záleží na webové aplikaci.

**Úroveň 5**
* Vazby názvu hostitele – závisí na certifikátu, pokud je k dispozici. V opačném případě závisí na prostředek vyšší úrovně.
* Rozšíření webu – závisí na nastavení konfigurace, pokud je k dispozici. V opačném případě závisí na prostředek vyšší úrovně.

Vaše řešení obvykle obsahuje pouze některé z těchto prostředků a vrstev. Pro chybějící úrovně namapujte nižší prostředky na další vyšší úroveň.

Následující příklad ukazuje část šablony. Hodnota konfigurace připojovacího řetězce závisí na rozšíření MSDeploy. Rozšíření MSDeploy závisí na webové aplikaci a databázi. 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Ukázka připravená ke spuštění, která používá výše uvedený kód, najdete v [tématu Šablona: Vytvoření jednoduché webové aplikace Umbraco](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple)Web App .

## <a name="find-information-about-msdeploy-errors"></a>Vyhledání informací o chybách MSDeploy

Pokud šablona Správce prostředků používá MSDeploy, chybové zprávy nasazení může být obtížné pochopit. Chcete-li získat další informace po neúspěšném nasazení, vyzkoušejte následující kroky:

1. Přejděte na web ovou [konzoli Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console).
2. Přejděte do složky d:\home\LogFiles\SiteExtensions\MSDeploy.
3. Vyhledejte soubory appManagerStatus.xml a appManagerLog.xml. První soubor protokoluje stav. Druhý soubor protokoluje informace o chybě. Pokud vám chyba není jasná, můžete ji zahrnout, když žádáte o pomoc na [fóru](https://docs.microsoft.com/answers/topics/azure-webapps.html).

## <a name="choose-a-unique-web-app-name"></a>Výběr jedinečného názvu webové aplikace

Název webové aplikace musí být globálně jedinečný. Můžete použít konvence pojmenování, která je pravděpodobně jedinečný, nebo můžete použít [funkci uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) pomoci s generováním jedinečný název.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Nasazení certifikátu webové aplikace z trezoru klíčů

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud vaše šablona obsahuje prostředek [Microsoft.Web/certificates](/azure/templates/microsoft.web/certificates) pro vazbu SSL a certifikát je uložen v trezoru klíčů, musíte se ujistit, že identita služby App Service má přístup k certifikátu.

V globálním Azure má instanční objekt služby App Service ID **abfa0a7c-a6b6-4736-8310-5855508787cd**. Chcete-li udělit přístup k trezoru klíčů pro instanční objekt služby Služby App Service, použijte:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

Ve službě Azure Government má instanční objekt služby App Service ID **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Použijte toto ID v předchozím příkladu.

V trezoru klíčů vyberte certifikáty a **Vygenerujte nebo importujte** certifikát. **Certificates**

![Import certifikátu](media/web-sites-rm-template-guidance/import-certificate.png)

V šabloně zadejte název certifikátu `keyVaultSecretName`pro .

Ukázkovou šablonu najdete [v tématu Nasazení certifikátu webové aplikace z tajného klíče trezoru klíčů a jeho použití k vytvoření vazby SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault).

## <a name="next-steps"></a>Další kroky

* Kurz nasazení webových aplikací se šablonou najdete v tématu [Zřizování a nasazování mikroslužeb předvídatelně v Azure](deploy-complex-application-predictably.md).
* Informace o syntaxi JSON a vlastnostech typů prostředků v šablonách najdete v [tématu Odkaz na šablonu Správce prostředků Azure](/azure/templates/).
