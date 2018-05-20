---
title: Zabezpečení clusteru se systémem Windows pomocí zabezpečení systému Windows | Microsoft Docs
description: Naučte se konfigurovat – uzly a klientský uzel zabezpečení v samostatné clusteru se systémem Windows pomocí zabezpečení systému Windows.
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
ms.openlocfilehash: 0f0df7883b25344560514491c08af3eadf872ffb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Zabezpečení samostatné clusteru v systému Windows pomocí zabezpečení systému Windows
K zabránění neoprávněného přístupu k cluster Service Fabric, je nutné zabezpečit clusteru. Zabezpečení je zvlášť důležité při spuštění úlohy v produkčním prostředí clusteru. Tento článek popisuje, jak nakonfigurovat zabezpečení – uzly a uzlu klientů pomocí zabezpečení systému Windows v *souboru ClusterConfig.JSON* souboru.  Proces odpovídá krok konfigurace zabezpečení [vytvořit samostatnou clusteru se systémem Windows](service-fabric-cluster-creation-for-windows-server.md). Další informace o tom, jak Service Fabric používá zabezpečení systému Windows najdete v tématu [clusteru scénáře zabezpečení](service-fabric-cluster-security.md).

> [!NOTE]
> Výběr mezi uzly zabezpečení byste měli zvážit pečlivě vzhledem k tomu, že neexistuje žádná upgrade clusteru z výběru jeden zabezpečení do jiného. Chcete-li změnit výběr zabezpečení, budete muset znovu sestavit úplnou clusteru.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Konfigurace zabezpečení systému Windows pomocí gMSA  
Ukázka *ClusterConfig.gMSA.Windows.MultiMachine.JSON* konfigurační soubor stažené společně se sadou [Microsoft.Azure.ServiceFabric.WindowsServer.<version>. ZIP](http://go.microsoft.com/fwlink/?LinkId=730690) samostatný cluster balíček obsahuje šablony pro konfiguraci zabezpečení systému Windows pomocí [skupinový účet spravované služby (gMSA)](https://technet.microsoft.com/library/hh831782.aspx):  

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
| ClusterCredentialType |Nastavte na *Windows* povolit zabezpečení systému Windows pro komunikaci mezi uzly.  | 
| ServerCredentialType |Nastavte na *Windows* povolit zabezpečení systému Windows pro komunikaci klienta uzlu. |  
| WindowsIdentities |Obsahuje identity clusteru a klientů. |  
| ClustergMSAIdentity |Nakonfiguruje zabezpečení mezi uzly. Skupinu účet spravované služby. |  
| ClusterSPN |Registrovaný název SPN pro účet gMSA|  
| ClientIdentities |Nakonfiguruje klienta uzel zabezpečení. Pole klienta uživatelské účty. | 
| Identita |Přidejte uživatele domény, doména\uživatelské jméno pro identitu klienta. |  
| IsAdmin |Nastavte na hodnotu true, chcete-li určit, zda má uživatel domény přístup správce klienta, nebo hodnotu NEPRAVDA pro klientský přístup uživatele. |  

[Uzel zabezpečení uzlu](service-fabric-cluster-security.md#node-to-node-security) je nakonfigurované nastavením **ClustergMSAIdentity** když service fabric potřebuje pro spuštění pod gMSA. Chcete-li vytvořit vztahy důvěryhodnosti mezi uzly, se musí být provedeny vědět navzájem. Můžete to provést dvěma způsoby: Skupinový účet spravované služby zahrnující všechny uzly v clusteru nebo zadejte skupinu domény počítače, který obsahuje všechny uzly v clusteru. Důrazně doporučujeme pomocí [skupinový účet spravované služby (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) přístup, zejména pro větší clustery (víc než 10 uzlů) nebo pro clustery, které by mohly zvětšení nebo zmenšení.  
Tento přístup nevyžaduje vytváření skupiny domény, na který byla udělena Správce clusterů přístupová práva k přidání a odebrání členů. Tyto účty jsou také užitečná pro automatickou správu hesel. Další informace najdete v tématu [Začínáme s skupinové účty spravované služby](http://technet.microsoft.com/library/jj128431.aspx).  
 
[Klient uzel zabezpečení](service-fabric-cluster-security.md#client-to-node-security) je konfigurován pomocí **ClientIdentities**. Chcete-li vytvořit vztah důvěryhodnosti mezi klientem a cluster, je nutné nakonfigurovat clusteru potřebujete vědět, který klient identity, které můžete důvěřovat. To můžete provést dvěma způsoby: Zadejte skupinu uživatele domény, které můžete připojit nebo zadat uzlu uživatele domény, které se můžou připojit. Service Fabric podporuje dva typy ovládacích prvků různý přístup pro klienty, kteří jsou připojené ke clusteru Service Fabric: správce a uživatele. Řízení přístupu poskytuje možnost pro správce clusteru k omezení přístupu k určitým typům operace clusteru pro různé skupiny uživatelů, lepší zabezpečení clusteru.  Správci mají plný přístup k funkcím správy (včetně možnosti pro čtení i zápis). Uživatelé, ve výchozím nastavení, mají pouze pro čtení přístup k možnosti správy (například možnosti dotazu) a možnost řešení aplikace a služby. Další informace o řízení přístupu najdete v tématu [řízení přístupu podle rolí pro Service Fabric klienty](service-fabric-cluster-security-roles.md).  
 
Následující příklad **zabezpečení** části nakonfiguruje zabezpečení systému Windows pomocí gMSA a určuje, že počítače v *ServiceFabric.clusterA.contoso.com* gMSA jsou součástí clusteru a že *CONTOSO\usera* má klient přístup správce:  
  
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
  
## <a name="configure-windows-security-using-a-machine-group"></a>Konfigurace zabezpečení systému Windows ve skupině počítač  
Tento model je zastaralá. Doporučuje se pro používání gMSA podle popisu výše. Ukázka *ClusterConfig.Windows.MultiMachine.JSON* konfigurační soubor stažené společně se sadou [Microsoft.Azure.ServiceFabric.WindowsServer.<version>. ZIP](http://go.microsoft.com/fwlink/?LinkId=730690) samostatný cluster balíček obsahuje šablony pro konfiguraci zabezpečení systému Windows.  Zabezpečení systému Windows je nakonfigurovaný v **vlastnosti** části: 

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
| ClusterCredentialType |Nastavte na *Windows* povolit zabezpečení systému Windows pro komunikaci mezi uzly.  | 
| ServerCredentialType |Nastavte na *Windows* povolit zabezpečení systému Windows pro komunikaci klienta uzlu. |  
| WindowsIdentities |Obsahuje identity clusteru a klientů. |  
| ClusterIdentity |Název skupiny počítače, domain\machinegroup, slouží ke konfiguraci zabezpečení mezi uzly. |  
| ClientIdentities |Nakonfiguruje klienta uzel zabezpečení. Pole klienta uživatelské účty. |  
| Identita |Přidejte uživatele domény, doména\uživatelské jméno pro identitu klienta. |  
| IsAdmin |Nastavte na hodnotu true, chcete-li určit, zda má uživatel domény přístup správce klienta, nebo hodnotu NEPRAVDA pro klientský přístup uživatele. |  

[Uzel zabezpečení uzlu](service-fabric-cluster-security.md#node-to-node-security) se konfiguruje pomocí nastavení **ClusterIdentity** Pokud chcete použít skupinu počítače v doméně služby Active Directory. Další informace najdete v tématu [vytvořit skupinu počítače ve službě Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).

[Uzel Klient zabezpečení](service-fabric-cluster-security.md#client-to-node-security) se konfiguruje pomocí **ClientIdentities**. K vybudování důvěry mezi klientem a cluster, je nutné nakonfigurovat clusteru potřebujete vědět, klient identity, které můžete důvěřovat clusteru. Můžete vytvořit vztah důvěryhodnosti v dvěma různými způsoby:

- Zadejte skupinu uživatele domény, které se můžou připojit.
- Zadejte uživatele uzlu domény, které se můžou připojit.

Service Fabric podporuje dva typy ovládacích prvků různý přístup pro klienty, kteří jsou připojené ke clusteru Service Fabric: správce a uživatele. Řízení přístupu umožňuje omezit přístup pro určité typy operací clusteru pro různé skupiny uživatelů, takže je bezpečnější clusteru pomocí Správce clusteru.  Správci mají plný přístup k funkcím správy (včetně možnosti pro čtení i zápis). Uživatelé, ve výchozím nastavení, mají pouze pro čtení přístup k možnosti správy (například možnosti dotazu) a možnost řešení aplikace a služby.  

Následující příklad **zabezpečení** části nakonfiguruje zabezpečení systému Windows, určuje, že počítače v *ServiceFabric/clusterA.contoso.com* jsou součástí clusteru a určuje, že *CONTOSO\usera* má klient přístup správce:

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
> Service Fabric nesmí být nasazen na řadiči domény. Ujistěte se, že souboru ClusterConfig.json při použití skupiny počítačů obsahující IP adresu řadiče domény nebo skupinový účet spravované služby (gMSA).
>
>

## <a name="next-steps"></a>Další postup
Po dokončení konfigurace zabezpečení systému Windows v *souboru ClusterConfig.JSON* souboru, pokračovat v procesu vytváření clusteru [vytvořit samostatnou clusteru se systémem Windows](service-fabric-cluster-creation-for-windows-server.md).

Další informace o tom, jak-uzly zabezpečení, klientský uzel zabezpečení a řízení přístupu na základě rolí, najdete v tématu [clusteru scénáře zabezpečení](service-fabric-cluster-security.md).

V tématu [připojení ke clusteru zabezpečené](service-fabric-connect-to-secure-cluster.md) příklady připojení pomocí prostředí PowerShell nebo FabricClient.
