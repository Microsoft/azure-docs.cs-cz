---
title: Zabezpečení clusteru spuštěného v systému Windows pomocí zabezpečení systému Windows
description: Learn how to configure node-to-node and client-to-node security on a standalone cluster running on Windows by using Windows security.
author: dkkapur
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: 46be6acc1ef08770826a2e020c8930eba0787791
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774449"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Zabezpečení samostatného clusteru v systému Windows pomocí zabezpečení systému Windows
Chcete-li zabránit neoprávněnému přístupu ke clusteru Service Fabric, je nutné cluster zabezpečit. Zabezpečení je důležité zejména při clusteru běží produkční úlohy. Tento článek popisuje, jak nakonfigurovat zabezpečení mezi uzly a klientem a uzlem pomocí zabezpečení systému Windows v souboru *ClusterConfig.JSON.*  Tento proces odpovídá kroku konfigurace v části [Vytvoření samostatného clusteru spuštěného v systému Windows](service-fabric-cluster-creation-for-windows-server.md). Další informace o tom, jak service fabric používá zabezpečení systému Windows, naleznete [v tématu Scénáře zabezpečení clusteru](service-fabric-cluster-security.md).

> [!NOTE]
> Výběr zabezpečení mezi uzlinami byste měli pečlivě zvážit, protože neexistuje žádný upgrade clusteru z jedné volby zabezpečení na jinou. Chcete-li změnit výběr zabezpečení, je třeba znovu vytvořit celý cluster.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Konfigurace zabezpečení systému Windows pomocí technologie gMSA  
Ukázkový konfigurační soubor *ClusterConfig.gMSA.Windows.MultiMachine.JSON* stažený pomocí serveru [Microsoft.Azure.ServiceFabric.WindowsServer.\< Balíček](https://go.microsoft.com/fwlink/?LinkId=730690) samostatného clusteru verze>.zip obsahuje šablonu pro konfiguraci zabezpečení systému Windows pomocí [účtu spravované služby skupiny (gMSA):](https://technet.microsoft.com/library/hh831782.aspx)  

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
| Typ pověření clusteru |Chcete-li povolit zabezpečení systému Windows pro komunikaci uzlů uzlů, nastavte na *systém Windows.*  | 
| Typ pověření serveru |Chcete-li povolit zabezpečení systému Windows pro komunikaci mezi klientským uzly, nastavte na *systém Windows.* |
| WindowsIdentities |Obsahuje identity clusteru a klienta. |
| ClustergMSAIdentity |Konfiguruje zabezpečení mezi uzlami. Účet služby spravované skupinou. |
| Název clusteruSPN |Registrovaný spn pro účet gMSA|
| Klientské identity |Konfiguruje zabezpečení mezi klientem a uzly. Pole klientských uživatelských účtů. |
| Identita |Přidejte pro identitu klienta uživatele domény, doménu\uživatelské jméno. |
| IsAdmin |Nastavte hodnotu true, chcete-li určit, že uživatel domény má přístup klienta správce nebo false pro přístup klienta uživatele. |

> [!NOTE]
> Hodnota ClustergMSAIdentity musí býtmysfgmsa@mydomainve formátu " ".

[Zabezpečení uzlu k uzlu](service-fabric-cluster-security.md#node-to-node-security) je nakonfigurováno nastavením **ClustergMSAIdentity,** když je třeba spustit infrastrukturu služeb pod gMSA. Aby bylo možné budovat vztahy důvěryhodnosti mezi uzly, musí být informováni o sobě navzájem. Toho lze dosáhnout dvěma různými způsoby: Zadejte účet spravované služby skupiny, který zahrnuje všechny uzly v clusteru, nebo Zadejte skupinu doménových strojů, která zahrnuje všechny uzly v clusteru. Důrazně doporučujeme používat přístup [účtu spravované služby skupiny (gMSA),](https://technet.microsoft.com/library/hh831782.aspx) zejména pro větší clustery (více než 10 uzlů) nebo pro clustery, které se pravděpodobně zvětší nebo zmenší.  
Tento přístup nevyžaduje vytvoření skupiny domén, pro kterou byla správcům clusteru udělena přístupová práva k přidávání a odebírání členů. Tyto účty jsou také užitečné pro automatickou správu hesel. Další informace naleznete [v tématu Začínáme s účty spravovaných služeb skupiny](https://technet.microsoft.com/library/jj128431.aspx).  
 
[Zabezpečení klienta k uzlu](service-fabric-cluster-security.md#client-to-node-security) je konfigurováno pomocí **identit ClientIdentities**. Chcete-li vytvořit vztah důvěryhodnosti mezi klientem a clusterem, je nutné nakonfigurovat cluster tak, aby věděl, kterým klientským identitám může důvěřovat. To lze provést dvěma různými způsoby: Zadejte uživatele skupiny domén, kteří se mohou připojit nebo určit uživatele uzlů domény, kteří se mohou připojit. Service Fabric podporuje dva různé typy řízení přístupu pro klienty, kteří jsou připojeni k clusteru Service Fabric: správce a uživatel. Řízení přístupu umožňuje správci clusteru omezit přístup k určitým typům operací clusteru pro různé skupiny uživatelů, čímž se cluster ujistí.  Správci mají plný přístup k možnostem správy (včetně možností čtení a zápisu). Uživatelé mají ve výchozím nastavení pouze přístup pro čtení k možnostem správy (například možnosti dotazů) a možnost řešení aplikací a služeb. Další informace o řízení přístupu naleznete v [tématu Řízení přístupu na základě rolí pro klienty Service Fabric](service-fabric-cluster-security-roles.md).  
 
Následující ukázková část **zabezpečení** konfiguruje zabezpečení systému Windows pomocí technologie gMSA a určuje, že počítače v *ServiceFabric.clusterA.contoso.com* gMSA jsou součástí clusteru a že *contoso\usera* má přístup klienta správce:  
  
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
  
## <a name="configure-windows-security-using-a-machine-group"></a>Konfigurace zabezpečení systému Windows pomocí skupiny strojů  
Tento model je zastaralá. Doporučení je použít gMSA, jak je podrobně popsáno výše. Ukázkový konfigurační soubor *ClusterConfig.Windows.MultiMachine.JSON* stažený pomocí [serveru\< Microsoft.Azure.ServiceFabric.WindowsServer. balíček](https://go.microsoft.com/fwlink/?LinkId=730690) samostatného clusteru verze>.zip obsahuje šablonu pro konfiguraci zabezpečení systému Windows.  Zabezpečení systému Windows je nakonfigurováno v části **Vlastnosti:** 

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
| Typ pověření clusteru |Chcete-li povolit zabezpečení systému Windows pro komunikaci uzlů uzlů, nastavte na *systém Windows.*  |
| Typ pověření serveru |Chcete-li povolit zabezpečení systému Windows pro komunikaci mezi klientským uzly, nastavte na *systém Windows.* |
| WindowsIdentities |Obsahuje identity clusteru a klienta. |
| Identita clusteru |Ke konfiguraci zabezpečení mezi uzlinami použijte název skupiny počítače domain\machinegroup. |
| Klientské identity |Konfiguruje zabezpečení mezi klientem a uzly. Pole klientských uživatelských účtů. |  
| Identita |Přidejte pro identitu klienta uživatele domény, doménu\uživatelské jméno. |  
| IsAdmin |Nastavte hodnotu true, chcete-li určit, že uživatel domény má přístup klienta správce nebo false pro přístup klienta uživatele. |  

[Zabezpečení uzlu k uzlu](service-fabric-cluster-security.md#node-to-node-security) je nakonfigurováno nastavením pomocí **clusteru Identity,** pokud chcete použít skupinu počítače v rámci domény služby Active Directory. Další informace naleznete [v tématu Vytvoření skupiny strojů ve službě Active Directory](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx).

[Zabezpečení klient-uzel](service-fabric-cluster-security.md#client-to-node-security) je konfigurováno pomocí **služby ClientIdentities**. Chcete-li vytvořit vztah důvěryhodnosti mezi klientem a clusterem, je nutné nakonfigurovat cluster tak, aby znal identity klienta, kterým může cluster důvěřovat. Důvěru můžete vytvořit dvěma různými způsoby:

- Zadejte uživatele skupiny domén, kteří se mohou připojit.
- Zadejte uživatele uzlů domény, kteří se mohou připojit.

Service Fabric podporuje dva různé typy řízení přístupu pro klienty, kteří jsou připojeni k clusteru Service Fabric: správce a uživatel. Řízení přístupu umožňuje správci clusteru omezit přístup k určitým typům operací clusteru pro různé skupiny uživatelů, což zajišťuje větší zabezpečení clusteru.  Správci mají plný přístup k možnostem správy (včetně možností čtení a zápisu). Uživatelé mají ve výchozím nastavení pouze přístup pro čtení k možnostem správy (například možnosti dotazů) a možnost řešení aplikací a služeb.  

Následující ukázková část **zabezpečení** konfiguruje zabezpečení systému Windows, určuje, že počítače v *ServiceFabric/clusterA.contoso.com* jsou součástí clusteru, a určuje, že *contoso\usera* má přístup klienta správce:

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
> Service Fabric by neměl být nasazen na řadiči domény. Ujistěte se, že clusterConfig.json neobsahuje IP adresu řadiče domény při použití skupiny počítačů nebo skupiny účet spravované služby (gMSA).
>
>

## <a name="next-steps"></a>Další kroky
Po konfiguraci zabezpečení systému Windows v souboru *ClusterConfig.JSON* pokračujte v procesu vytváření clusteru v [části Vytvoření samostatného clusteru spuštěného v systému Windows](service-fabric-cluster-creation-for-windows-server.md).

Další informace o zabezpečení mezi uzly, zabezpečení klient-uzel a řízení přístupu založené na rolích naleznete v [tématu Scénáře zabezpečení clusteru](service-fabric-cluster-security.md).

Příklady připojení pomocí PowerShellu nebo FabricClient najdete v [tématu Připojení k zabezpečenému clusteru.](service-fabric-connect-to-secure-cluster.md)
