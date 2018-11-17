---
title: Azure Service Fabric nastavit reverzní proxy server | Dokumentace Microsoftu
description: Naučte se nastavit a konfigurace reverzního proxy serveru Service Fabric.
services: service-fabric
documentationcenter: na
author: jimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 07/27/201
ms.author: v-jamebr
ms.openlocfilehash: 2f84550c83c646b44f4a59c3ae506df7c18d1555
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852975"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Nastavení a konfigurace reverzního proxy serveru v Azure Service Fabric
Reverzní proxy server je volitelnou službu Azure Service Fabric, která pomáhá mikroslužeb spouštěných v clusteru Service Fabric, zjistit a komunikovat s ostatními službami, které mají koncových bodů http. Další informace najdete v tématu [reverzní proxy server v Azure Service Fabric](service-fabric-reverseproxy.md). Tento článek ukazuje, jak nastavit a konfigurace reverzního proxy serveru v clusteru. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Povolit reverzní proxy server pomocí webu Azure portal

Azure portal poskytuje možnost povolit reverzní proxy server při vytváření nového clusteru Service Fabric. Nelze upgradovat existující cluster použít reverzní proxy server na portálu. 

Konfigurace reverzního proxy serveru při vám [vytvoření clusteru pomocí webu Azure portal](./service-fabric-cluster-creation-via-portal.md), ujistěte se, postupujte takto:

1. V **krok 2: Konfigurace clusteru**v části **konfigurace typu uzlu**vyberte **povolit reverzní proxy server**.

   ![Povolit reverzní proxy server na portálu](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. (Volitelné) Konfigurace zabezpečeného reverzní proxy server, budete muset nakonfigurovat certifikát SSL. V **krok 3: zabezpečení**na **konfigurovat nastavení zabezpečení clusteru**v části **typ konfigurace**vyberte **vlastní**. Potom v části **certifikát reverzního proxy serveru SSL**vyberte **zahrnout certifikát SSL pro reverzní proxy server** a zadejte podrobnosti o certifikátu.

   ![Konfigurace zabezpečeného reverzního proxy serveru na portálu](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Pokud se rozhodnete konfigurace reverzního proxy serveru pomocí certifikátu, při vytváření clusteru, můžete tak učinit později pomocí šablony Resource Manageru pro skupinu prostředků clusteru. Další informace najdete v tématu [povolit reverzní proxy server pomocí šablon Azure Resource Manageru](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Povolit reverzní proxy server pomocí šablon Azure Resource Manageru

Pro clustery v Azure můžete použít šablony Azure Resource Manageru povolit reverzní proxy server v Service Fabric. Můžete povolit reverzní proxy server při vytváření clusteru nebo povolit aktualizací clusteru kdykoli později. 

Pro nový cluster, můžete [vytvoření vlastní šablony Resource Manageru](service-fabric-cluster-creation-via-arm.md) nebo můžete pomocí ukázkové šablony. 

Ukázkové šablony Resource Manageru, které vám umožňují nakonfigurovat zabezpečené reverzního proxy serveru pro clusteru v Azure můžete najít [zabezpečené reverzního proxy serveru ukázkové šablony](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample) na Githubu. Odkazovat na [konfigurace HTTPS reverzní proxy server v zabezpečený cluster](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) v souboru README pro pokyny a šablony pomocí konfigurace reverzní proxy server zabezpečený pomocí certifikátu a zpracovat certifikát výměny.

Pro existující cluster, můžete exportovat šablonu Resource Manageru pro prostředek clusteru skupinou pomocí [webu Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template#export-the-template-from-resource-group), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell#export-resource-group-as-template), nebo [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli#export-resource-group-as-template).

Jakmile budete mít šablony Resource Manageru, můžete povolit reverzní proxy server pomocí následujících kroků:

1. Definovat port pro reverzní proxy server v [sekci parametrů](../azure-resource-manager/resource-group-authoring-templates.md) šablony.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Zadejte port pro každý typ nodetype objektů v [ **Microsoft.ServiceFabric/clusters** ](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [části typu prostředku](../azure-resource-manager/resource-group-authoring-templates.md).

    Port, který je určený podle názvu parametru, reverseProxyEndpointPort.

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```
3. Chcete-li nakonfigurovat certifikáty SSL pro reverzní proxy server na portu, přidejte certifikát, který chcete ***reverseProxyCertificate*** vlastnost v **Microsoft.ServiceFabric/clusters** [prostředků Zadejte části](../resource-group-authoring-templates.md).

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Podpůrný certifikát reverzního proxy serveru, který se liší od certifikátu clusteru
 Pokud certifikát reverzního proxy serveru se liší od certifikátu, který zabezpečuje clusteru, pak dříve zadaný certifikát by měl být nainstalovaný na virtuálním počítači a přidat do seznamu řízení přístupu (ACL) tak, aby k němu mají přístup Service Fabric. To můžete udělat [ **Microsoft.Compute/virtualMachineScaleSets** ](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) [části typu prostředku](../resource-group-authoring-templates.md). Pro instalaci přidejte certifikát do osProfile. Rozšíření část šablony můžete aktualizovat certifikát v seznamu ACL.

  ```json
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    ....
      "osProfile": {
          "adminPassword": "[parameters('adminPassword')]",
          "adminUsername": "[parameters('adminUsername')]",
          "computernamePrefix": "[parameters('vmNodeType0Name')]",
          "secrets": [
            {
              "sourceVault": {
                "id": "[parameters('sfReverseProxySourceVaultValue')]"
              },
              "vaultCertificates": [
                {
                  "certificateStore": "[parameters('sfReverseProxyCertificateStoreValue')]",
                  "certificateUrl": "[parameters('sfReverseProxyCertificateUrlValue')]"
                }
              ]
            }
          ]
        }
   ....
   "extensions": [
          {
              "name": "[concat(parameters('vmNodeType0Name'),'_ServiceFabricNode')]",
              "properties": {
                      "type": "ServiceFabricNode",
                      "autoUpgradeMinorVersion": false,
                      ...
                      "publisher": "Microsoft.Azure.ServiceFabric",
                      "settings": {
                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                        "dataPath": "D:\\\\SvcFab",
                        "durabilityLevel": "Bronze",
                        "testExtension": true,
                        "reverseProxyCertificate": {
                          "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                          "x509StoreName": "[parameters('sfReverseProxyCertificateStoreValue')]"
                        },
                  },
                  "typeHandlerVersion": "1.0"
              }
          },
      ]
    }
  ```
> [!NOTE]
> Pokud používáte certifikáty, které se liší od certifikátu clusteru povolit reverzní proxy server v existujícím clusteru, nainstalujte certifikát reverzního proxy serveru a aktualizovat seznam ACL v clusteru povolit reverzní proxy server. Dokončení [šablony Azure Resource Manageru](service-fabric-cluster-creation-via-arm.md) nasazení s použitím nastavení uvedeného dříve než zahájíte nasazení povolit reverzní proxy server v kroků 1 – 3.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Povolit reverzní proxy server na samostatné clustery

Pro samostatné clustery povolit reverzní proxy server v souboru ClusterConfig.json. Můžete povolit reverzní proxy server při vytváření clusteru nebo upgradem konfiguraci existujícího clusteru. Další informace o nastaveních, která je k dispozici v souborech ClusterConfig.json najdete v tématu [nastavení samostatného clusteru](./service-fabric-cluster-manifest.md).

Následující kroky ukazují, nastavení používat k povolení reverzního proxy serveru a volitelně také zajistit reverzní proxy server pomocí certifikátu X.509. 

1. Chcete-li povolit reverzní proxy, nastavte **reverseProxyEndpointPort** hodnota pro typ uzlu v rámci **vlastnosti** v konfiguraci clusteru. Následující kód JSON ukazuje nastavení reverzní proxy port koncového bodu na 19081 pro uzly s typem "NodeType0":

   ```json
       "properties": {
          ... 
           "nodeTypes": [
               {
                   "name": "NodeType0",
                   ...
                   "reverseProxyEndpointPort": "19081",
                   ...
               }
           ],
          ...
       }
   ```
2. (Volitelné) Pro zabezpečené reverzního proxy serveru, konfiguraci certifikátu v **zabezpečení** části **vlastnosti**. 
   - Pro vývojové nebo testovací prostředí, můžete použít **ReverseProxyCertificate** nastavení:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificate": {
                          "Thumbprint": "[Thumbprint]",
                          "ThumbprintSecondary": "[Thumbprint]",
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```
   - Pro produkční prostředí **ReverseProxyCertificateCommonNames** natavení je doporučené:

      ```json
          "properties": {
              ...
              "security": {
                  ...
                  "CertificateInformation": {
                      ...
                      "ReverseProxyCertificateCommonNames": {
                        "CommonNames": [
                            {
                              "CertificateCommonName": "[CertificateCommonName]"
                            }
                          ],
                          "X509StoreName": "My"
                      },
                      ...
                  }
              },
              ...
          }
      ```

   Další informace o konfiguraci a správě certifikátů pro samostatný cluster, stejně jako podrobnější informace o konfiguraci certifikátů používaných pro zabezpečenou reverzního proxy serveru najdete v tématu [X509 zabezpečení na základě certifikátů](./service-fabric-windows-cluster-x509-security.md).

Poté, co jste upravili soubor ClusterConfig.json povolit reverzní proxy server, postupujte podle pokynů v [upgradovat konfiguraci clusteru](service-fabric-cluster-config-upgrade-windows-server.md) nasdílejte změny do vašeho clusteru.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Vystavit na veřejném portu prostřednictvím Azure Load Balancer reverzního proxy serveru

K vyřešení reverzního proxy serveru z mimo Azure cluster, nastavte pravidla nástroje pro vyrovnávání zatížení Azure a Azure sonda stavu služby pro port reverzního proxy serveru. Tyto kroky můžete provést kdykoli po vytvoření clusteru pomocí webu Azure portal nebo šablony Resource Manageru. 

> [!WARNING]
> Když nakonfigurujete v nástroji pro vyrovnávání zatížení port reverzního proxy, všechny mikroslužby v clusteru, která zpřístupňují koncový bod HTTP adresovatelných z mimo cluster. To znamená, že mikroslužeb má být interní může být zjistitelné pomocí určené uživatel se zlými úmysly. Tato potenially uvede závažné ohrožení zabezpečení, které je někdo zneužije; Příklad:
>
> * Uživatel se zlými úmysly může spustit útoku DoS opakovaně voláním interní služba, která nemá dostatečně odolné útoky.
> * Uživatel se zlými úmysly může poskytovat chybně formovaným paketům interní služba následek nežádoucí chování.
> * Služba má být interní může vrátit privátní nebo citlivé informace, které nejsou určeny zpřístupní ke službám mimo cluster, tak vystavení citlivých informací k uživateli se zlými úmysly. 
>
> Ujistěte se, že plně pochopit a zmírnění potenciální bezpečnostní důsledky pro váš cluster a aplikace spuštěny, před zahájením zveřejnit port reverzního proxy. 
>

Pokud chcete vystavit reverzní proxy server veřejně pro samostatný cluster, způsobem, ve kterém tak učiníte, bude záviset na systému, hostování clusteru a je nad rámec tohoto článku. Předchozí upozornění na vystavení reverzní proxy server veřejně, nicméně je relevantní.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Vystavení reverzní proxy server pomocí webu Azure portal 

1. Na portálu Azure portal klikněte na skupinu prostředků pro váš cluster a pak klikněte na nástroj pro vyrovnávání zatížení pro váš cluster.
2. Chcete-li přidat stav testu paměti pro port reverzního proxy serveru, v levém podokně okna nástroje pro vyrovnávání zatížení v části **nastavení**, klikněte na tlačítko **sondy stavu**. Pak klikněte na tlačítko **přidat** v horní části stav sond okna a zadejte podrobnosti pro port reverzního proxy serveru a pak klikněte na **OK**. Výchozí port reverzního proxy je 19081, pokud jste nezměnili při vytváření clusteru.

   ![Konfigurace sondy stavu reverzního proxy serveru](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Chcete-li přidat pravidlo služby Load balancer úrovně můžete zveřejnit port reverzního proxy serveru, v levém podokně okna nástroje pro vyrovnávání zatížení v části **nastavení**, klikněte na tlačítko **pravidla Vyrovnávání zatížení**. Pak klikněte na tlačítko **přidat** v horní části zatížení vyrovnávání pravidla okna a zadejte podrobnosti pro port reverzního proxy serveru. Ujistěte se, že nastavíte **Port** hodnotu na port reverzního proxy serveru, které jsou zveřejněné na, chcete **back-endový port** hodnotu, která jste nastavili při povolování reverzního proxy serveru, port a **sondu stavu** hodnota, která má sondu stavu, který jste nakonfigurovali v předchozím kroku. Nastavit další pole, jako příslušné a klikněte na tlačítko **OK**.

   ![Konfigurace pravidla služby load balancer pro reverzní proxy server](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Vystavení reverzního proxy serveru pomocí šablony Resource Manageru

Následující kód JSON odkazuje na stejnou šablonu, která se používá v [povolit reverzní proxy server pomocí šablon Azure Resource Manageru](#enable-reverse-proxy-via-azure-resource-manager-templates). Přečtěte si tuto část dokumentu informace o tom, jak vytvořit šablonu Resource Manageru nebo exportu šablony pro existující cluster.  Změny provedené [ **Microsoft.Network/loadBalancers** ](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers) [části typu prostředku](../resource-group-authoring-templates.md).

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Přizpůsobení chování reverzní proxy server pomocí nastavení prostředků infrastruktury

Můžete přizpůsobit chování reverzního proxy serveru prostřednictvím nastavení prostředků infrastruktury v šabloně Resource Manageru pro clustery hostovaných v Azure nebo v souboru ClusterConfig.json pro samostatné clustery. Nastavení, která řídí chování reverzního proxy serveru jsou umístěny v [ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) tématu **nastavení fabricSettings** části clusteru **vlastnosti** oddílu. 

Například můžete nastavit hodnotu **DefaultHttpRequestTimeout** nastavit časový limit pro požadavky na reverzní proxy server na 180 sekund stejně jako v následujícím kódu JSON:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "DefaultHttpRequestTimeout",
                   "value": "180"
                 }
               ]
             }
           ],
           ...
   }
   ``` 

Další informace o aktualizaci nastavení prostředků infrastruktury pro clustery Azure najdete v tématu [přizpůsobit nastavení clusteru pomocí šablon Resource Manageru](service-fabric-cluster-config-upgrade-azure.md). Samostatné clustery, naleznete v tématu [přizpůsobit nastavení clusteru pro samostatné clustery](service-fabric-cluster-config-upgrade-windows-server.md). 

Několik nastavení prostředků infrastruktury slouží k navázání zabezpečené komunikace mezi reverzního proxy serveru a služby. Podrobné informace o těchto nastavení najdete v tématu [připojení k službě zabezpečené pomocí reverzního proxy serveru](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>Další postup
* [Nastavení předávání do zabezpečené služba HTTP přes reverzní proxy](service-fabric-reverseproxy-configure-secure-communication.md)
* Možnosti konfigurace reverzního proxy serveru najdete v tématu [části ApplicationGateway/Http v nastavení clusteru Service Fabric přizpůsobit](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).
