---
title: Azure Service Fabric nastavení reverzního proxy serveru
description: Naučte se, jak nastavit a nakonfigurovat službu reverzního proxy serveru pro aplikaci Azure Service Fabric.
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: pepogors
ms.openlocfilehash: f8a9025a50b2815f0e6030e7baf317b261c8c462
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86256341"
---
# <a name="set-up-and-configure-reverse-proxy-in-azure-service-fabric"></a>Nastavení a konfigurace reverzního proxy serveru v Azure Service Fabric
Reverzní proxy je volitelná služba Azure Service Fabric, která pomáhá mikroslužbám běžícím v clusteru Service Fabric zjišťovat a komunikovat s dalšími službami, které mají koncové body http. Další informace najdete v tématu [reverzní proxy v Azure Service Fabric](service-fabric-reverseproxy.md). V tomto článku se dozvíte, jak nastavit a nakonfigurovat reverzní proxy server v clusteru. 

## <a name="enable-reverse-proxy-using-azure-portal"></a>Povolení reverzního proxy serveru pomocí Azure Portal

Azure Portal poskytuje možnost Povolit reverzní proxy server při vytváření nového clusteru Service Fabric. Stávající cluster nemůžete upgradovat na použití reverzního proxy serveru prostřednictvím portálu. 

Pokud chcete konfigurovat reverzní proxy server, když [vytvoříte cluster pomocí Azure Portal](./service-fabric-cluster-creation-via-portal.md), ujistěte se, že jste provedli následující akce:

1. V **kroku 2: konfigurace clusteru** v části **typ uzlu Konfigurace** vyberte **Povolit reverzní proxy server**.

   ![Povolit reverzní proxy na portálu](./media/service-fabric-reverseproxy-setup/enable-rp-portal.png)
2. Volitelné Ke konfiguraci zabezpečeného reverzního proxy serveru musíte nakonfigurovat certifikát TLS/SSL. V **kroku 3: zabezpečení** v části **Konfigurace nastavení zabezpečení clusteru** v části **typ konfigurace** vyberte možnost **vlastní**. Pak v části **certifikát SSL pro reverzní proxy** vyberte **Zahrnout certifikát SSL pro reverzní proxy** a zadejte podrobnosti o certifikátu.

   ![Konfigurace zabezpečeného reverzního proxy serveru na portálu](./media/service-fabric-reverseproxy-setup/configure-rp-certificate-portal.png)

   Pokud se rozhodnete nekonfigurovat reverzní proxy s certifikátem při vytváření clusteru, můžete to provést později prostřednictvím šablony Správce prostředků pro skupinu prostředků clusteru. Další informace najdete v tématu [Povolení reverzního proxy prostřednictvím šablon Azure Resource Manager](#enable-reverse-proxy-via-azure-resource-manager-templates).

## <a name="enable-reverse-proxy-via-azure-resource-manager-templates"></a>Povolení reverzního proxy prostřednictvím šablon Azure Resource Manager

Pro clustery v Azure můžete použít šablonu Azure Resource Manager k povolení reverzního proxy serveru v Service Fabric. Můžete povolit reverzní proxy server, když vytvoříte cluster, nebo ho povolíte tak, že později aktualizujete cluster. 

Pro nový cluster můžete [vytvořit vlastní šablonu správce prostředků](service-fabric-cluster-creation-via-arm.md) nebo můžete použít vzorovou šablonu. 

Můžete najít ukázkovou Správce prostředků šablony, které vám pomůžou nakonfigurovat zabezpečený reverzní proxy pro cluster Azure na GitHubu v rámci [šablony pro zabezpečení reverzního proxy serveru](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample) . Pokyny a šablony, které se použijí ke konfiguraci zabezpečeného serveru reverzního proxy s certifikátem a ke zpracování změn certifikátů, najdete v tématu [Konfigurace reverzního proxy serveru HTTPS v zabezpečeném clusteru](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample/README.md#configure-https-reverse-proxy-in-a-secure-cluster) v souboru Readme.

Pro existující cluster můžete exportovat šablonu Správce prostředků pro skupinu prostředků clusteru pomocí [Azure Portal](../azure-resource-manager/templates/export-template-portal.md), [PowerShellu](../azure-resource-manager/management/manage-resources-powershell.md)nebo rozhraní příkazového [řádku Azure](../azure-resource-manager/management/manage-resources-cli.md).

Po vytvoření šablony Správce prostředků můžete povolit reverzní proxy pomocí následujících kroků:

1. V [části parametry](../azure-resource-manager/templates/template-syntax.md) v šabloně definujte port reverzního proxy serveru.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19081,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Zadejte port pro každý objekt NodeType v [části typ prostředku](../azure-resource-manager/templates/template-syntax.md) [**Microsoft. ServiceFabric/clustery**](/azure/templates/microsoft.servicefabric/clusters) .

    Port je identifikovaný názvem parametru reverseProxyEndpointPort.

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
3. Chcete-li nakonfigurovat certifikáty TLS/SSL na portu reverzního proxy serveru, přidejte certifikát do vlastnosti ***reverseProxyCertificate** _ v části _ *Microsoft. ServiceFabric/clustery* *  [typ prostředku](../azure-resource-manager/templates/template-syntax.md).

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

### <a name="supporting-a-reverse-proxy-certificate-thats-different-from-the-cluster-certificate"></a>Podpora certifikátu reverzního proxy serveru, který se liší od certifikátu clusteru
 Pokud se certifikát reverzního proxy liší od certifikátu, který zabezpečuje cluster, pak by měl být dříve zadaný certifikát na virtuálním počítači nainstalovaný a přidaný do seznamu řízení přístupu (ACL), aby k němu Service Fabric mít přístup. To se dá udělat v [části typ prostředku](../azure-resource-manager/templates/template-syntax.md) [**Microsoft. COMPUTE/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets) . Pro instalaci přidejte certifikát do osProfile. Oddíl Extension v šabloně může aktualizovat certifikát v seznamu ACL.

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
> Pokud používáte certifikáty, které se liší od certifikátu clusteru, pokud chcete povolit reverzní proxy server v existujícím clusteru, nainstalujte certifikát reverzního proxy serveru a před povolením reverzního proxy serveru aktualizujte seznam ACL na clusteru. Dokončete nasazení [šablony Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) pomocí výše zmíněného nastavení a před zahájením nasazení povolte reverzní proxy v krocích 1-3.

## <a name="enable-reverse-proxy-on-standalone-clusters"></a>Povolení reverzního proxy serveru na samostatných clusterech

Pro samostatné clustery povolte reverzní proxy v souboru ClusterConfig.js. Můžete povolit reverzní proxy server při vytváření clusteru nebo upgradovat konfiguraci pro existující cluster. Další informace o nastaveních dostupných v ClusterConfig.jssouborů najdete v tématu věnovaném [Nastavení samostatného clusteru](./service-fabric-cluster-manifest.md).

Následující kroky ukazují nastavení, která se použijí pro povolení reverzního proxy serveru a volitelně k zabezpečení reverzního proxy serveru pomocí certifikátu X. 509. 

1. Chcete-li povolit reverzní proxy, nastavte hodnotu **reverseProxyEndpointPort** pro typ uzlu v části **vlastnosti** v konfiguraci clusteru. Následující JSON ukazuje nastavení portu koncového bodu reverzního proxy na 19081 pro uzly s typem "NodeType0":

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
2. Volitelné V případě zabezpečeného reverzního proxy serveru nakonfigurujte certifikát v části **zabezpečení** v části **vlastnosti**. 
   - Pro vývojové nebo testovací prostředí můžete použít nastavení **ReverseProxyCertificate** :

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
   - V produkčním prostředí se doporučuje nastavení **ReverseProxyCertificateCommonNames** :

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

   Další informace o konfiguraci a správě certifikátů pro samostatný cluster a o dalších podrobnostech o konfiguraci certifikátů používaných k zabezpečení reverzního proxy serveru najdete v tématu [zabezpečení založené na certifikátech x509](./service-fabric-windows-cluster-x509-security.md).

Po úpravě ClusterConfig.jssouboru, aby bylo možné povolit reverzní proxy, postupujte podle pokynů v části [Upgrade konfigurace clusteru](service-fabric-cluster-config-upgrade-windows-server.md) pro vložení změn do clusteru.


## <a name="expose-reverse-proxy-on-a-public-port-through-azure-load-balancer"></a>Vystavení reverzního proxy serveru na veřejném portu prostřednictvím Azure Load Balancer

Pokud chcete reverzní proxy server vyřešit mimo cluster Azure, nastavte pravidla Azure Load Balancer a sondu stavu Azure pro port reverzního proxy serveru. Tyto kroky můžete provést kdykoli po vytvoření clusteru pomocí Azure Portal nebo šablony Správce prostředků. 

> [!WARNING]
> Když nakonfigurujete port reverzního proxy serveru v Load Balancer, všechny mikroslužby v clusteru, které zveřejňují koncový bod HTTP, se budou adresovat mimo cluster. To znamená, že mikroslužby, které mají být interní, mohou být zjistitelné zjištěným uživatelem se zlými úmysly. To potenciálně představuje závažné chyby zabezpečení, které je možné zneužít; například:
>
> * Uživatel se zlými úmysly může spustit útok s cílem odepření služby Opakovaným voláním interní služby, která nemá dostatečně posílenou plochu pro útoky.
> * Uživatel se zlými úmysly může doručovat poškozené pakety do interní služby, což vede k neúmyslnému chování.
> * Služba, která má být interní, může vracet soukromé nebo citlivé informace, které nejsou určené k zpřístupnění službám mimo cluster, takže tyto citlivé informace vystavuje uživatel se zlými úmysly. 
>
> Ujistěte se, že plně rozumíte a zmírnit potenciální důsledky zabezpečení pro váš cluster a aplikace, které jsou v něm spuštěné, a teprve potom zajistěte veřejný port reverzního proxy serveru. 
>

Pokud chcete server reverzního proxy zveřejnit jako samostatný cluster, bude způsob, jakým tak učiníte, záviset na systému, který je hostitelem clusteru, a nad rámec tohoto článku. Předchozí upozornění na veřejně vystavení reverzního proxy serveru se však stále používá.

### <a name="expose-the-reverse-proxy-using-azure-portal"></a>Zpřístupnění reverzního proxy serveru pomocí Azure Portal 

1. Na Azure Portal klikněte na skupinu prostředků clusteru a pak klikněte na nástroj pro vyrovnávání zatížení pro váš cluster.
2. Pokud chcete přidat sondu stavu pro port reverzního proxy serveru, klikněte v levém podokně okna nástroje pro vyrovnávání zatížení v části **Nastavení** na **sondy stavu**. Pak klikněte na tlačítko **Přidat** v horní části okna sondy stavu a zadejte podrobnosti o portu reverzního proxy serveru a klikněte na tlačítko **OK**. Ve výchozím nastavení je port reverzního proxy 19081, pokud jste ho nezměnili při vytváření clusteru.

   ![Konfigurace testu stavu reverzního proxy](./media/service-fabric-reverseproxy-setup/lb-rp-probe.png)
3. Pokud chcete přidat pravidlo Load Balancer pro vystavení portu reverzního proxy serveru, v levém podokně okna nástroje pro vyrovnávání zatížení v části **Nastavení** klikněte na **pravidla vyrovnávání zatížení**. Pak v horní části okna pravidla vyrovnávání zatížení klikněte na **Přidat** a zadejte podrobnosti pro port reverzního proxy serveru. Ujistěte se, že jste nastavili hodnotu **portu** na port, na který chcete, aby byl reverzní proxy server vystavený, hodnota **portu back-endu** na port, který jste nastavili, když jste povolili reverzní proxy, a hodnotu **sondy stavu** pro test stavu, který jste nakonfigurovali v předchozím kroku. Podle potřeby nastavte další pole a klikněte na tlačítko **OK**.

   ![Konfigurace pravidla nástroje pro vyrovnávání zatížení pro reverzní proxy](./media/service-fabric-reverseproxy-setup/lb-rp-rule.png)

### <a name="expose-the-reverse-proxy-via-resource-manager-templates"></a>Zpřístupnění reverzního proxy serveru prostřednictvím šablon Správce prostředků

Následující JSON odkazuje na stejnou šablonu, která se používá v [Povolení reverzního proxy prostřednictvím šablon Azure Resource Manager](#enable-reverse-proxy-via-azure-resource-manager-templates). Informace o tom, jak vytvořit šablonu Správce prostředků nebo jak exportovat šablonu pro existující cluster, najdete v této části dokumentu.  Změny se provedou v [části typ prostředku](../azure-resource-manager/templates/template-syntax.md) [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers) .

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

Můžete přizpůsobit chování reverzního proxy serveru prostřednictvím nastavení prostředků infrastruktury v šabloně Správce prostředků pro clustery hostované v Azure nebo v ClusterConfig.jssouborů pro samostatné clustery. Nastavení, které řídí chování reverzního proxy serveru, se nachází v části [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) v části **fabricSettings** v části věnované **vlastnostem** clusteru. 

Například můžete nastavit hodnotu **DefaultHttpRequestTimeout** , abyste nastavili časový limit pro požadavky na reverzní proxy na 180 sekund, jak je uvedeno v následujícím formátu JSON:

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

Další informace o aktualizaci nastavení prostředků infrastruktury pro clustery Azure najdete v tématu [přizpůsobení nastavení clusteru pomocí Správce prostředků šablon](service-fabric-cluster-config-upgrade-azure.md). Samostatné clustery najdete v tématu [přizpůsobení nastavení clusteru pro samostatné clustery](service-fabric-cluster-config-upgrade-windows-server.md). 

K navázání zabezpečené komunikace mezi reverzním proxy serverem a službami se používá několik nastavení prostředků infrastruktury. Podrobné informace o těchto nastaveních najdete v tématu [připojení k zabezpečené službě pomocí reverzního proxy serveru](service-fabric-reverseproxy-configure-secure-communication.md).

## <a name="next-steps"></a>Další kroky
* [Nastavení přesměrování na zabezpečenou službu HTTP pomocí reverzního proxy serveru](service-fabric-reverseproxy-configure-secure-communication.md)
* Možnosti konfigurace reverzního proxy serveru najdete [v části ApplicationGateway/http tématu přizpůsobení Service Fabricho nastavení clusteru](service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).
