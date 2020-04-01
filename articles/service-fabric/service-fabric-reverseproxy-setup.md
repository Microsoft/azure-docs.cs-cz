---
title: Azure Service Fabric nastavit reverzní proxy server
description: Zjistěte, jak nastavit a nakonfigurovat službu reverzní proxy pro aplikaci Azure Service Fabric.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: pepogors
ms.openlocfilehash: 6e3edb0fe238dcaddb7d99cc68660591f081581c
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476667"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Nastavení a konfigurace reverzního proxy serveru ve službě Azure Service Fabric
Reverzní proxy server je volitelná služba Azure Service Fabric, která pomáhá mikroslužbám spuštěným v clusteru Service Fabric zjišťovat a komunikovat s dalšími službami, které mají koncové body http. Další informace najdete [v tématu Reverzní proxy server ve službě Azure Fabric](service-fabric-reverseproxy.md). Tento článek ukazuje, jak nastavit a nakonfigurovat reverzní proxy server v clusteru. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Povolení reverzního proxy serveru pomocí portálu Azure

Portál Azure poskytuje možnost povolit reverzní proxy server při vytváření nového clusteru Service Fabric. Nelze upgradovat existující cluster pro použití reverzní proxy prostřednictvím portálu. 

Chcete-li při [vytváření clusteru pomocí portálu Azure](./service-fabric-cluster-creation-via-portal.md)nakonfigurovat reverzní proxy server , postupujte takto:

1. V **kroku 2: Konfigurace clusteru**včásti **Konfigurace typu uzlu**vyberte **povolit reverzní proxy server**.

   ![Povolení reverzního proxy serveru na portálu](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. (Nepovinné) Chcete-li nakonfigurovat zabezpečený reverzní proxy server, je třeba nakonfigurovat certifikát TLS/SSL. V **kroku 3: Zabezpečení**v části Configure cluster security **settings**vyberte v části **Configuration type**vyberte **Custom**. Potom v části **Reverse Proxy SSL certificate**vyberte Zahrnout certifikát **SSL pro reverzní proxy server** a zadejte podrobnosti o certifikátu.

   ![Konfigurace zabezpečeného reverzního proxy serveru na portálu](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Pokud se při vytváření clusteru rozhodnete nekonfigurovat reverzní proxy server s certifikátem, můžete tak učinit později prostřednictvím šablony Správce prostředků pro skupinu prostředků clusteru. Další informace najdete v [tématu Povolení reverzního proxy serveru pomocí šablon Azure Resource Manager .](#enable-reverse-proxy-via-azure-resource-manager-templates)

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Povolení reverzního proxy serveru pomocí šablon Azure Resource Manageru

Pro clustery v Azure můžete použít šablonu Azure Resource Manager k povolení reverzníproxy v Service Fabric. Reverzní proxy server můžete povolit při vytváření clusteru nebo jej povolit pozdější aktualizací clusteru. 

Pro nový cluster můžete [vytvořit vlastní šablonu Správce prostředků](service-fabric-cluster-creation-via-arm.md) nebo můžete použít ukázkovou šablonu. 

Ukázkové šablony Správce prostředků, které vám pomůžou nakonfigurovat zabezpečený reverzní proxy server pro cluster Azure, najdete v [ukázkových šablonách zabezpečeného reverzního proxy serveru](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample) na GitHubu. Pokyny a šablony, které chcete použít ke konfiguraci zabezpečeného reverzního proxy serveru s certifikátem a ke zpracování efektu přechodu certifikátu, naleznete v části [Konfigurace reverzního proxy serveru HTTPS v zabezpečeném clusteru](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) v zabezpečeném clusteru.

Pro existující cluster můžete exportovat šablonu Správce prostředků pro skupinu prostředků clusteru pomocí [portálu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template), [PowerShellu](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell)nebo [nastavení příkazového příkazu k webu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli).

Po vytvoření šablony Správce prostředků můžete povolit reverzní proxy server pomocí následujících kroků:

1. Definujte port pro reverzní proxy server v [části Parametry](../azure-resource-manager/templates/template-syntax.md) šablony.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Zadejte port pro každý z objektů typu nodetype v části [**Microsoft.ServiceFabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [Resource type](../azure-resource-manager/templates/template-syntax.md).

    Port je identifikován názvem parametru reverseProxyEndpointPort.

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
3. Chcete-li nakonfigurovat certifikáty TLS/SSL na portu pro reverzní proxy server, přidejte certifikát do vlastnosti ***reverseProxyCertificate*** v části **Microsoft.ServiceFabric/clusters** [Resource Type](../resource-group-authoring-templates.md).

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

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Podpora reverzního proxy certifikátu, který se liší od clusterového certifikátu
 Pokud se reverzní proxy certifikát liší od certifikátu, který zabezpečuje cluster, měl by být dříve zadaný certifikát nainstalován ve virtuálním počítači a přidán do seznamu řízení přístupu (ACL), aby k němu service fabric měl přistupovat. To lze provést v části [**Microsoft.Compute/virtualMachineScaleSets**](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachinescalesets) [Typ prostředku](../resource-group-authoring-templates.md). Pro instalaci přidejte certifikát do osProfile. Část rozšíření šablony můžete aktualizovat certifikát v acl.

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
> Pokud k povolení reverzního proxy serveru v existujícím clusteru použijete certifikáty, které se liší od certifikátu clusteru, nainstalujte zpětný proxy certifikát a před povolením reverzního proxy serveru nainstalujte certifikát a aktualizujte acl ACL v clusteru. Dokončete nasazení [šablony Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) pomocí nastavení uvedených výše před spuštěním nasazení povolit reverzní proxy server v krocích 1-3.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Povolení reverzního proxy serveru v samostatných clusterech

U samostatných clusterů povolíte reverzní proxy server v souboru ClusterConfig.json. Reverzní proxy server můžete povolit při vytváření clusteru nebo upgradem konfigurace pro existující cluster. Další informace o nastaveních dostupných v souborech ClusterConfig.json naleznete v [tématu Nastavení samostatného clusteru](./service-fabric-cluster-manifest.md).

Následující kroky ukazují nastavení, které se má použít k povolení reverzního proxy serveru a volitelně k zabezpečení reverzního proxy serveru pomocí certifikátu X.509. 

1. Chcete-li povolit reverzní proxy, nastavte hodnotu **reverseProxyEndpointPort** pro typ uzlu pod **vlastnostmi** v konfiguraci clusteru. Následující JSON zobrazuje nastavení portu koncového bodu reverzní proxy na 19081 pro uzly s typem "NodeType0":

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
2. (Nepovinné) Pro zabezpečený reverzní proxy server nakonfigurujte certifikát v části **zabezpečení** v části **vlastnosti**. 
   - Pro vývojové nebo testovací prostředí můžete použít nastavení **ReverseProxyCertificate:**

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
   - Pro produkční prostředí se doporučuje nastavení **ReverseProxyCertificateCommonNames:**

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

   Další informace o konfiguraci a správě certifikátů pro samostatný cluster a další podrobnosti o konfiguraci certifikátů používaných k zabezpečení reverzního proxy serveru naleznete v [tématu Zabezpečení založené na certifikátech X509](./service-fabric-windows-cluster-x509-security.md).

Po úpravě souboru ClusterConfig.json tak, aby umožňoval reverzní proxy server, postupujte podle pokynů v [části Upgrade konfigurace clusteru](service-fabric-cluster-config-upgrade-windows-server.md) a přenesete změny do clusteru.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Vystavit reverzní proxy na veřejném portu prostřednictvím Azure Load Balancer

Chcete-li řešit reverzní proxy z mimo cluster Azure, nastavte pravidla Azure Load Balancer a Azure Health Probe pro reverzní proxy port. Tyto kroky lze provést pomocí portálu Azure nebo šablony Správce prostředků kdykoli po vytvoření clusteru. 

> [!WARNING]
> Při konfiguraci portu reverzní proxy v nástroj pro vyrovnávání zatížení, všechny mikroslužby v clusteru, které zveřejňují koncový bod HTTP jsou adresovatelné z mimo clusteru. To znamená, že mikroslužby, které mají být interní, mohou být zjistitelné určeným uživatelem se zlými úmysly. To potenciálně představuje závažné chyby zabezpečení, které lze zneužít; například:
>
> * Uživatel se zlými úmysly může spustit útok odmítnutí služby opakovaným voláním interní služby, která nemá dostatečně zpevněný prostor pro útok.
> * Uživatel se zlými úmysly může doručovat poškozené pakety do interní služby, což vede k neúmyslnému chování.
> * Služba, která má být interní, může vrátit soukromé nebo citlivé informace, které nejsou určeny k vystavení službám mimo cluster, a tím vystavit tyto citlivé informace uživateli se zlými úmysly. 
>
> Ujistěte se, že plně pochopit a zmírnit potenciální důsledky zabezpečení pro váš cluster a aplikace spuštěné na něm, než uděláte reverzní proxy port veřejné. 
>

Pokud chcete veřejně vystavit reverzní proxy server pro samostatný cluster, způsob, ve kterém tak učiníte, bude záviset na systému hostujícím cluster a je nad rámec tohoto článku. Předchozí upozornění o vystavení reverzní proxy veřejně, ale stále platí.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Vystavit reverzní proxy server pomocí portálu Azure 

1. Na webu Azure Portal klikněte na skupinu prostředků pro váš cluster a potom klikněte na balancer zatížení pro váš cluster.
2. Chcete-li přidat sondu stavu pro port reverzní proxy, klikněte v levém podokně okna vyrovnávání zatížení v části **NASTAVENÍ**na **položku Sondy stavu**. Pak klikněte na **Přidat** v horní části okna sondstavu a zadejte podrobnosti pro reverzní proxy port a pak klepněte na tlačítko **OK**. Ve výchozím nastavení je reverzní port proxy 19081, pokud jste jej při vytváření clusteru nezměnili.

   ![Konfigurace sondy stavu reverzního proxy serveru](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Chcete-li přidat pravidlo pro vyrovnávání zatížení, které zpřístupní reverzní port proxy, klepněte v levém podokně okna vyrovnávání zatížení v části **NASTAVENÍ**na **položku Pravidla vyrovnávání zatížení**. Pak klikněte na **Přidat** v horní části okna pravidla vyrovnávání zatížení a zadejte podrobnosti pro reverzní proxy port. Ujistěte se, že jste nastavili hodnotu **portu** na port, na kterém má být server zpětného proxy serveru vystaven, hodnotu **portu Back-end** na port, který jste nastavili při povolení reverzního proxy serveru, a hodnotu **sondy stavu** na sondě stavu, kterou jste nakonfigurovali v předchozím kroku. Podle potřeby nastavte další pole a klepněte na tlačítko **OK**.

   ![Konfigurace pravidla pro vyrovnávání zatížení pro reverzní proxy server](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Vystavit reverzní proxy server prostřednictvím šablon Správce prostředků

Následující JSON odkazuje na stejnou šablonu, která se používá v [povolit reverzní proxy prostřednictvím šablon Azure Resource Manager](#enable-reverse-proxy-via-azure-resource-manager-templates). Informace o vytvoření šablony Správce prostředků nebo exportu šablony pro existující cluster naleznete v této části dokumentu.  Změny jsou provedeny v části [**Microsoft.Network/loadBalancers**](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers) [Resource type](../resource-group-authoring-templates.md).

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


## <a name="customize-reverse-proxy-behavior-using-fabric-settings"></a>Přizpůsobení chování reverzního proxy serveru pomocí nastavení prostředků infrastruktury

Chování reverzního proxy serveru můžete přizpůsobit pomocí nastavení prostředků v šabloně Správce prostředků pro clustery hostované v Azure nebo v souboru ClusterConfig.json pro samostatné clustery. Nastavení, která řídí chování reverzního proxy serveru, jsou umístěna v části [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) v části **fabricSettings** v části **Vlastnosti** clusteru. 

Můžete například nastavit hodnotu **DefaultHttpRequestTimeout** tak, aby nastavila časový limit pro požadavky na reverzní proxy server na 180 sekund jako v následujícím json:

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

Další informace o aktualizaci nastavení prostředků infrastruktury pro clustery Azure najdete v [tématu Přizpůsobení nastavení clusteru pomocí šablon Správce prostředků](service-fabric-cluster-config-upgrade-azure.md). Samostatné clustery naleznete v [tématu Přizpůsobení nastavení clusteru pro samostatné clustery](service-fabric-cluster-config-upgrade-windows-server.md). 

Několik nastavení prostředků infrastruktury se používají k vytvoření zabezpečené komunikace mezi reverzní proxy server a služby. Podrobné informace o těchto nastaveních naleznete [v tématu Připojení k zabezpečené službě pomocí reverzního proxy serveru](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>Další kroky
* [Nastavení předávání pro zabezpečení služby HTTP pomocí reverzního proxy serveru](service-fabric-reverseproxy-configure-secure-communication.md)
* Možnosti konfigurace reverzního proxy serveru naleznete [v části ApplicationGateway/Http v tématu Přizpůsobení nastavení clusteru Service Fabric](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).
