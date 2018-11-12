---
title: Zabezpečení clusteru běžícího na Windows s použitím Windows zabezpečení | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat uzel uzlu a uzel klienta zabezpečení samostatného clusteru běžícího na Windows s využitím zabezpečení Windows.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: 1775eb4659ccc71d962d0beab9b605e01eb12f72
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253614"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Zabezpečení samostatného clusteru ve Windows pomocí zabezpečení Windows
Chcete-li zabránit neoprávněnému přístupu ke clusteru Service Fabric, třeba zabezpečení clusteru. Zabezpečení je obzvláště důležité, když v clusteru běží úlohy v produkčním prostředí. Tento článek popisuje postup konfigurace zabezpečení mezi uzly a uzel klienta s použitím Windows zabezpečení v *ClusterConfig.JSON* souboru.  Proces odpovídá kroku konfigurovat zabezpečení [vytvoření samostatného clusteru se systémem Windows](service-fabric-cluster-creation-for-windows-server.md). Další informace o tom, jak Service Fabric používá zabezpečení Windows, naleznete v tématu [scénáře zabezpečení clusteru](service-fabric-cluster-security.md).

> [!NOTE]
> Výběr zabezpečení mezi uzly měli zvážit opatrně, protože neexistuje žádný upgrade clusteru z výběru jednoho zabezpečení do jiného. Chcete-li změnit výběr zabezpečení, budete muset znovu sestavit úplný clusteru.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Konfigurace zabezpečení Windows využívajícími gMSA  
Ukázka *ClusterConfig.gMSA.Windows.MultiMachine.JSON* konfigurační soubor stáhne společně s [Microsoft.Azure.ServiceFabric.WindowsServer.<version>. ZIP](https://go.microsoft.com/fwlink/?LinkId=730690) samostatného clusteru balíčku obsahuje šablonu konfigurace pomocí zabezpečení Windows [skupinový účet spravované služby (gMSA)](https://technet.microsoft.com/library/hh831782.aspx):  

```  
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {  
                "ClustergMSAIdentity": "[gMSA Identity]", 
                "ClusterSPN": "[Registered SPN for the gMSA account]",
                "ClientIdentities": [  
                    {  
                        "Identity": "domain\\username",  
                        "IsAdmin": true  
                    }  
                ]  
            }  
        }  
```  

| **Nastavení konfigurace** | **Popis** |
| --- | --- |
| ClusterCredentialType |Nastavte na *Windows* povolit zabezpečení Windows pro komunikaci mezi uzly.  | 
| ServerCredentialType |Nastavte na *Windows* povolit zabezpečení Windows pro komunikaci klienta uzlu. |  
| WindowsIdentities |Obsahuje identitu clusteru a klienta. |  
| ClustergMSAIdentity |Konfiguruje zabezpečení mezi uzly. Skupinový účet spravované služby. |  
| ClusterSPN |Registrovaný hlavní název služby pro účet gMSA|  
| ClientIdentities |Nakonfiguruje klienta mezi uzly zabezpečení. Pole klientské uživatelské účty. | 
| Identita |Přidejte uživatele domény, pro identity klienta doména\uživatelské jméno. |  
| IsAdmin |Nastavte na hodnotu true. Chcete-li určit, jestli má uživatel domény přístup správce klienta, nebo hodnotu NEPRAVDA pro klientský přístup uživatele. |  

[Uzel zabezpečení uzlu](service-fabric-cluster-security.md#node-to-node-security) je nakonfigurovaný tak, že nastavíte **ClustergMSAIdentity** když service fabric potřebuje ke spuštění v rámci gMSA. K vytvoření vztahů důvěryhodnosti mezi uzly, se musí být informován o sobě navzájem. To lze provést dvěma způsoby: Určuje skupinový účet spravované služby, který zahrnuje všechny uzly v clusteru nebo doménovou skupinu počítačů, která obsahuje všechny uzly v clusteru. Důrazně doporučujeme používat [skupinový účet spravované služby (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) přístup, zejména pro větší clustery (více než 10 uzlů) nebo pro clustery, které jsou pravděpodobně zvětšit nebo zmenšit.  
Tento postup nevyžaduje, aby vytváření skupiny domény, na kterém byla udělena Správce clusterů přístupová práva k přidávání a odebírání členů. Tyto účty jsou také užitečné pro automatickou správu hesel. Další informace najdete v tématu [Začínáme se službou skupinových účtů spravované služby](https://technet.microsoft.com/library/jj128431.aspx).  
 
[Klient zabezpečení uzlu](service-fabric-cluster-security.md#client-to-node-security) je nakonfigurovaný nástrojem **ClientIdentities**. Aby bylo možné navázat vztah důvěryhodnosti mezi klientem a clusterem, musí konfigurace clusteru vědět, který klient identity, které můžete důvěřovat. To můžete udělat dvěma různými způsoby: Zadejte skupinu uživatele domény, které můžete připojit nebo zadat uzlu uživatelé se můžou připojit. Service Fabric podporuje dva typy ovládacích prvků jiný přístup pro klienty, kteří jsou připojené ke clusteru Service Fabric: správce a uživatele. Řízení přístupu umožňuje omezit přístup k určitým typům operace clusteru pro různé skupiny uživatelů, lepší zabezpečení clusteru pomocí Správce clusteru.  Správci mají plný přístup k funkcím správy (včetně možností pro čtení a zápis). Uživatelé, ve výchozím nastavení, mají pouze přístup pro čtení k funkce pro správu (například schopnosti příkazů jazyka) a schopnost řešení aplikací a služeb. Další informace o řízení přístupu najdete v tématu [řízení přístupu podle rolí pro Service Fabric klienty](service-fabric-cluster-security-roles.md).  
 
Následující příklad **zabezpečení** části nakonfiguruje zabezpečení Windows využívajícími gMSA a určuje, že počítače v *ServiceFabric.clusterA.contoso.com* gMSA jsou součástí clusteru a že  *CONTOSO\usera* má přístup správce klienta:  
  
```  
"security": {
    "ClusterCredentialType": "Windows",            
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {  
        "ClustergMSAIdentity" : "ServiceFabric.clusterA.contoso.com",  
        "ClusterSPN" : "http/servicefabric/clusterA.contoso.com",  
        "ClientIdentities": [{  
            "Identity": "CONTOSO\\usera",  
            "IsAdmin": true  
        }]  
    }  
}  
```  
  
## <a name="configure-windows-security-using-a-machine-group"></a>Konfigurace zabezpečení Windows pomocí skupinu počítačů.  
Tento model je zastaralé. Doporučujeme pro používání gMSA podrobnosti jsou uvedené výše. Ukázka *ClusterConfig.Windows.MultiMachine.JSON* konfigurační soubor stáhne společně s [Microsoft.Azure.ServiceFabric.WindowsServer.<version>. ZIP](https://go.microsoft.com/fwlink/?LinkId=730690) samostatného clusteru balíčku obsahuje šablony pro konfiguraci zabezpečení Windows.  Zabezpečení Windows je nakonfigurovaný v **vlastnosti** části: 

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
                "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

| **Nastavení konfigurace** | **Popis** |
| --- | --- |
| ClusterCredentialType |Nastavte na *Windows* povolit zabezpečení Windows pro komunikaci mezi uzly.  | 
| ServerCredentialType |Nastavte na *Windows* povolit zabezpečení Windows pro komunikaci klienta uzlu. |  
| WindowsIdentities |Obsahuje identitu clusteru a klienta. |  
| ClusterIdentity |Použijte název skupiny počítače, domain\machinegroup, konfigurace zabezpečení mezi uzly. |  
| ClientIdentities |Nakonfiguruje klienta mezi uzly zabezpečení. Pole klientské uživatelské účty. |  
| Identita |Přidejte uživatele domény, pro identity klienta doména\uživatelské jméno. |  
| IsAdmin |Nastavte na hodnotu true. Chcete-li určit, jestli má uživatel domény přístup správce klienta, nebo hodnotu NEPRAVDA pro klientský přístup uživatele. |  

[Uzel zabezpečení uzlu](service-fabric-cluster-security.md#node-to-node-security) je nakonfigurovaný pomocí nastavení **ClusterIdentity** Pokud chcete použít skupinu počítačů v rámci domény služby Active Directory. Další informace najdete v tématu [vytvořit skupinu počítačů ve službě Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).

[Uzel klienta zabezpečení](service-fabric-cluster-security.md#client-to-node-security) se konfiguruje pomocí **ClientIdentities**. K navázání vztahu důvěryhodnosti mezi klientem a clusterem, musí konfigurace clusteru znát klienta identity, které můžete důvěřovat clusteru. Můžete vytvořit vztah důvěryhodnosti dvěma různými způsoby:

- Zadejte skupinu uživatele domény, se můžou připojit.
- Určení uživatelů domény uzlu, se můžou připojit.

Service Fabric podporuje dva typy ovládacích prvků jiný přístup pro klienty, kteří jsou připojené ke clusteru Service Fabric: správce a uživatele. Řízení přístupu umožňuje omezit přístup k určité typy operací clusteru pro různé skupiny uživatelů, což zajišťuje bezpečnější clusteru pomocí Správce clusteru.  Správci mají plný přístup k funkcím správy (včetně možností pro čtení a zápis). Uživatelé, ve výchozím nastavení, mají pouze přístup pro čtení k funkce pro správu (například schopnosti příkazů jazyka) a schopnost řešení aplikací a služeb.  

Následující příklad **zabezpečení** části nakonfiguruje Windows security, určuje, že počítače v *ServiceFabric/clusterA.contoso.com* jsou součástí clusteru a určuje, že *CONTOSO\usera* má přístup správce klienta:

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> Service Fabric by se neměly nasazovat na řadiči domény. Ujistěte se, že ClusterConfig.json při použití skupiny počítačů obsahující IP adresu řadiče domény nebo skupinový účet spravované služby (gMSA).
>
>

## <a name="next-steps"></a>Další postup
Po dokončení konfigurace zabezpečení Windows *ClusterConfig.JSON* souboru, pokračovat v procesu vytváření clusteru v [vytvoření samostatného clusteru se systémem Windows](service-fabric-cluster-creation-for-windows-server.md).

Další informace o způsobu-uzly zabezpečení, uzel klienta zabezpečení a řízení přístupu na základě rolí, najdete v tématu [scénáře zabezpečení clusteru](service-fabric-cluster-security.md).

Zobrazit [připojit k zabezpečenému clusteru](service-fabric-connect-to-secure-cluster.md) příklady připojení pomocí prostředí PowerShell nebo FabricClient.
