---
title: Nasazení aplikací pomocí šablon
description: Pokyny k vytváření šablon Azure Resource Manager pro zřizování a nasazení App Servicech aplikací.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 1146b5979d81b91c6c6894aa54b2e0ca50c896c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88961597"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Doprovodné materiály k nasazování webových aplikací pomocí šablon Azure Resource Manager

Tento článek poskytuje doporučení k vytváření Azure Resource Manager šablon pro nasazení Azure App Service řešení. Tato doporučení vám pomohou vyhnout se běžným problémům.

## <a name="define-dependencies"></a>Definovat závislosti

Definování závislostí pro webové aplikace vyžaduje porozumění způsobu interakce prostředků v rámci webové aplikace. Pokud zadáte závislosti v nesprávném pořadí, může dojít k chybám nasazení nebo vytvoření stavu časování, který zastavuje nasazení.

> [!WARNING]
> Pokud do šablony zahrnete rozšíření webového serveru, musíte nastavit všechny prostředky konfigurace tak, jak jsou závislé na prostředku MSDeploy. Změny konfigurace způsobí, že se lokalita znovu spustí asynchronně. Když provedete prostředky konfigurace závislé na MSDeploy, zajistíte, že se příkaz MSDeploy dokončí před restartováním lokality. Bez těchto závislostí může být lokalita restartována během procesu nasazení MSDeploy. Příklad šablony naleznete v tématu [šablona WordPress se závislostí nasazení webu](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

Následující obrázek znázorňuje pořadí závislostí pro různé prostředky App Service:

![Závislosti webové aplikace](media/web-sites-rm-template-guidance/web-dependencies.png)

Prostředky se nasazují v následujícím pořadí:

**Vrstva 1**
* App Service plán.
* Všechny ostatní související prostředky, jako jsou databáze nebo účty úložiště.

**Vrstva 2**
* Webová aplikace – závisí na plánu App Service.
* Instance Azure Application Insights, která cílí na serverovou farmu – závisí na plánu App Service.

**Vrstva 3**
* Správa zdrojového kódu – závisí na webové aplikaci.
* MSDeploy – rozšíření webu – závisí na webové aplikaci.
* Instance Azure Application Insights, která cílí na webovou aplikaci – závisí na webové aplikaci.

**Vrstva 4**
* App Service certifikát – závisí na správě zdrojového kódu nebo MSDeploy, pokud je přítomen. V opačném případě závisí na webové aplikaci.
* Nastavení konfigurace (připojovací řetězce, web.config hodnoty, nastavení aplikace) – závisí na správě zdrojového kódu nebo MSDeploy, pokud je přítomen. V opačném případě závisí na webové aplikaci.

**Vrstva 5**
* Vazby názvu hostitele – závisí na certifikátu, pokud je k dispozici. V opačném případě závisí na prostředku vyšší úrovně.
* Rozšíření webu – závisí na nastavení konfigurace, pokud je k dispozici. V opačném případě závisí na prostředku vyšší úrovně.

Řešení obvykle zahrnuje pouze některé z těchto prostředků a úrovní. U chybějících vrstev namapujte nižší prostředky na úroveň nejbližší vyšší.

Následující příklad ukazuje část šablony. Hodnota konfigurace připojovacího řetězce závisí na rozšíření MSDeploy. Přípona MSDeploy závisí na webové aplikaci a databázi. 

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

Ukázku připraveného kódu, který používá výše uvedený kód, najdete v tématu [Šablona: sestavení jednoduché webové aplikace v Umbraco](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>Najít informace o chybách MSDeploy

Pokud vaše Správce prostředků šablona používá MSDeploy, může být obtížné pochopit chybové zprávy nasazení. Pokud chcete získat další informace po neúspěšném nasazení, zkuste provést následující kroky:

1. Přejít na [konzolu Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console)webu.
2. Přejděte do složky na adrese D:\home\LogFiles\SiteExtensions\MSDeploy..
3. Vyhledejte soubory appManagerStatus.xml a appManagerLog.xml. První soubor zapíše stav. Druhý soubor protokoluje informace o chybě. Pokud chyba není pro vás nejasná, můžete ji zahrnout, až budete požádáni o nápovědu k [fóru](/answers/topics/azure-webapps.html).

## <a name="choose-a-unique-web-app-name"></a>Zvolit jedinečný název webové aplikace

Název vaší webové aplikace musí být globálně jedinečný. Můžete použít konvenci pojmenování, která bude pravděpodobně jedinečná, nebo můžete použít [funkci uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) pro pomoc s vygenerováním jedinečného názvu.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Nasadit certifikát webové aplikace z Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud vaše šablona obsahuje prostředek [Microsoft. Web/Certificates](/azure/templates/microsoft.web/certificates) pro vazbu TLS/SSL a certifikát je uložený v Key Vault, musíte se ujistit, že App Service identita může získat přístup k certifikátu.

V globálním Azure má App Service instančního objektu ID **abfa0a7c-a6b6-4736-8310-5855508787cd**. Chcete-li udělit přístup k Key Vault pro instanční objekt služby App Service, použijte:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

V Azure Government má App Service instančního objektu ID **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Použijte toto ID v předchozím příkladu.

V Key Vault vyberte **certifikáty** a **vygenerujte/importujte** pro nahrání certifikátu.

![Import certifikátu](media/web-sites-rm-template-guidance/import-certificate.png)

Do šablony zadejte název certifikátu pro `keyVaultSecretName` .

Příklad šablony najdete v tématu [Nasazení certifikátu webové aplikace z Key Vault tajného kódu a jeho použití k vytvoření vazby SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault).

## <a name="next-steps"></a>Další kroky

* Kurz nasazení webových aplikací se šablonou najdete v tématu popisujícím [zřizování a nasazování mikroslužeb v Azure](deploy-complex-application-predictably.md).
* Další informace o syntaxi a vlastnostech JSON pro typy prostředků v šablonách naleznete v tématu [Azure Resource Manager Reference k šabloně](/azure/templates/).