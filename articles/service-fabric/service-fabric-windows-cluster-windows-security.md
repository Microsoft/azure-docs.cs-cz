---
title: Zabezpečte cluster běžící v systému Windows pomocí zabezpečení systému Windows.
description: Přečtěte si, jak nakonfigurovat zabezpečení mezi uzly a klientem a uzlem na samostatném clusteru běžícím v systému Windows pomocí zabezpečení systému Windows.
ms.topic: conceptual
ms.date: 08/24/2017
ms.openlocfilehash: a34c7084a9faaf0d676d4f6c68da53b2bc84f01b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574607"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Zabezpečení samostatného clusteru ve Windows pomocí zabezpečení systému Windows
Aby se zabránilo neoprávněnému přístupu ke clusteru Service Fabric, musíte zabezpečit cluster. Zabezpečení je obzvláště důležité, když cluster spouští produkční úlohy. Tento článek popisuje, jak nakonfigurovat zabezpečení mezi uzly a klientem a uzlem pomocí zabezpečení systému Windows v *ClusterConfig.JSv* souboru.  Proces odpovídá kroku konfigurace zabezpečení [vytvoření samostatného clusteru běžícího v systému Windows](service-fabric-cluster-creation-for-windows-server.md). Další informace o tom, jak Service Fabric používá zabezpečení systému Windows, najdete v tématu [scénáře zabezpečení clusteru](service-fabric-cluster-security.md).

> [!NOTE]
> Měli byste pečlivě zvážit výběr zabezpečení mezi uzly, protože neprobíhá upgrade clusteru z jedné volby zabezpečení na jinou. Chcete-li změnit výběr zabezpečení, je nutné znovu sestavit úplný cluster.
>
>

## <a name="configure-windows-security-using-gmsa"></a>Konfigurace zabezpečení Windows pomocí gMSA  
gMSA je preferovaný model zabezpečení. Ukázka *ClusterConfig.gMSA.Windows.MultiMachine.JS* konfiguračního souboru staženého pomocí [Microsoft. Azure. ServiceFabric. windowsserver. \<version> .](https://go.microsoft.com/fwlink/?LinkId=730690) samostatný clusterový balíček zip obsahuje šablonu pro konfiguraci zabezpečení systému Windows pomocí [skupinového účtu spravované služby (gMSA)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)):  

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
| ClusterCredentialType |Nastavte na *Windows* , aby se povolilo zabezpečení Windows pro komunikaci mezi uzly.  | 
| ServerCredentialType |Nastavte na *Windows* , aby se povolilo zabezpečení Windows pro komunikaci klientů s klientským uzlem. |
| WindowsIdentities |Obsahuje cluster a identitu klientů. |
| ClustergMSAIdentity |Konfiguruje zabezpečení mezi uzly. Skupinový účet spravované služby. |
| ClusterSPN |Registrovaný hlavní název služby (SPN) pro účet gMSA|
| ClientIdentities |Nakonfiguruje zabezpečení mezi klienty a uzly. Pole uživatelských účtů klienta. |
| Identita |Přidejte uživatele domény (doména \ Uživatel) pro identitu klienta. |
| Správce |Nastavte na hodnotu true, pokud chcete, aby měl uživatel domény oprávnění správce pro přístup klienta k uživateli nebo false. |

> [!NOTE]
> Hodnota ClustergMSAIdentity musí být ve formátu " mysfgmsa@mydomain ".

[Zabezpečení uzlů na uzel](service-fabric-cluster-security.md#node-to-node-security) je nakonfigurované nastavením **ClustergMSAIdentity** , když Service Fabric potřebuje běžet pod gMSA. Aby bylo možné sestavovat vztahy důvěryhodnosti mezi uzly, je nutné, aby si je navzájem věděli. Toho lze dosáhnout dvěma různými způsoby: Určete skupinový účet spravované služby, který zahrnuje všechny uzly v clusteru, nebo zadejte skupinu počítačů, která zahrnuje všechny uzly v clusteru. Důrazně doporučujeme používat přístup [skupinového účtu spravované služby (gMSA)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) , zvláště u větších clusterů (více než 10 uzlů) nebo u clusterů, které se nejspíš zvětšují nebo zmenšují.  
Tento přístup nevyžaduje vytvoření skupiny domén, pro kterou se správcům clusteru udělila přístupová práva k přidávání a odebírání členů. Tyto účty jsou užitečné také pro automatickou správu hesel. Další informace najdete v tématu [Začínáme se skupinovými účty spravované služby](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)).  
 
[Zabezpečení klienta na uzel](service-fabric-cluster-security.md#client-to-node-security) je nakonfigurované pomocí **ClientIdentities**. Aby bylo možné navázat vztah důvěryhodnosti mezi klientem a clusterem, je nutné nakonfigurovat cluster tak, aby věděli, které identity klienta může důvěřovat. To se dá udělat dvěma různými způsoby: Určete uživatele skupiny domény, které se můžou připojit, nebo zadejte uživatele uzlu domény, které se můžou připojit. Service Fabric podporuje pro klienty, kteří jsou připojení ke clusteru Service Fabric, dva různé typy řízení přístupu: správce a uživatel. Řízení přístupu poskytuje správci clusteru možnost omezit přístup k určitým typům operací clusteru pro různé skupiny uživatelů a tím zvýšit zabezpečení clusteru.  Správci mají plný přístup k funkcím správy (včetně funkcí pro čtení a zápis). Uživatelé mají ve výchozím nastavení přístup jen pro čtení k funkcím pro správu (například možnosti dotazů) a možnost přeložit aplikace a služby. Další informace o ovládacích prvcích přístupu najdete v tématu [řízení přístupu na základě rolí pro klienty Service Fabric](service-fabric-cluster-security-roles.md).  
 
Následující příklad oddílu **zabezpečení** nakonfiguruje zabezpečení systému Windows pomocí gMSA a určí, že počítače ve *ServiceFabric.clusterA.contoso.com* gMSA jsou součástí clusteru a že *CONTOSO\usera* má přístup klienta správce:  
  
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
  
## <a name="configure-windows-security-using-a-machine-group"></a>Konfigurace zabezpečení systému Windows pomocí skupiny počítačů  
Jak je popsáno výše, gMSA je upřednostňováno, ale je také podporováno pro použití tohoto modelu zabezpečení. Ukázka *ClusterConfig.Windows.MultiMachine.JS* konfiguračního souboru staženého pomocí [Microsoft. Azure. ServiceFabric. windowsserver. \<version> .](https://go.microsoft.com/fwlink/?LinkId=730690) samostatný clusterový balíček zip obsahuje šablonu pro konfiguraci zabezpečení systému Windows.  Zabezpečení systému Windows je konfigurováno v části **Properties (vlastnosti** ): 

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
| ClusterCredentialType |Nastavte na *Windows* , aby se povolilo zabezpečení Windows pro komunikaci mezi uzly.  |
| ServerCredentialType |Nastavte na *Windows* , aby se povolilo zabezpečení Windows pro komunikaci klientů s klientským uzlem. |
| WindowsIdentities |Obsahuje cluster a identitu klientů. |
| ClusterIdentity |Pomocí názvu skupiny počítačů domain\machinegroup nakonfigurujte zabezpečení mezi uzly. |
| ClientIdentities |Nakonfiguruje zabezpečení mezi klienty a uzly. Pole uživatelských účtů klienta. |  
| Identita |Přidejte uživatele domény (doména \ Uživatel) pro identitu klienta. |  
| Správce |Nastavte na hodnotu true, pokud chcete, aby měl uživatel domény oprávnění správce pro přístup klienta k uživateli nebo false. |  

[Zabezpečení uzlů na uzel](service-fabric-cluster-security.md#node-to-node-security) se konfiguruje nastavením použití **ClusterIdentity** , pokud chcete použít skupinu počítačů v rámci doména služby Active Directory. Další informace najdete v tématu [Vytvoření skupiny počítačů ve službě Active Directory](/previous-versions/commerce-server/aa545347(v=cs.70)).

[Zabezpečení klient-uzel](service-fabric-cluster-security.md#client-to-node-security) se konfiguruje pomocí **ClientIdentities**. Chcete-li vytvořit vztah důvěryhodnosti mezi klientem a clusterem, je nutné nakonfigurovat cluster tak, aby znal identity klientů, které může cluster důvěřovat. Vztah důvěryhodnosti můžete vytvořit dvěma různými způsoby:

- Určete uživatele skupiny domény, které se mohou připojit.
- Zadejte uživatele uzlu domény, které se mohou připojit.

Service Fabric podporuje pro klienty, kteří jsou připojení ke clusteru Service Fabric, dva různé typy řízení přístupu: správce a uživatel. Řízení přístupu umožňuje správci clusteru omezit přístup k určitým typům operací clusteru pro různé skupiny uživatelů, což zajistí vyšší zabezpečení clusteru.  Správci mají plný přístup k funkcím správy (včetně funkcí pro čtení a zápis). Uživatelé mají ve výchozím nastavení přístup jen pro čtení k funkcím pro správu (například možnosti dotazů) a možnost přeložit aplikace a služby.  

Následující příklad oddílu **zabezpečení** nakonfiguruje zabezpečení systému Windows, určuje, že počítače v *ServiceFabric/clusteru. contoso. com* jsou součástí clusteru, a určuje, že *CONTOSO\usera* má přístup klienta správce:

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
> Service Fabric by se neměla nasadit na řadič domény. Ujistěte se, že ClusterConfig.json nezahrnuje IP adresu řadiče domény při použití skupiny počítačů nebo skupinového účtu spravované služby (gMSA).
>
>

## <a name="next-steps"></a>Další kroky
Po nakonfigurování zabezpečení systému Windows v *ClusterConfig.JSv* souboru pokračujte v procesu vytváření clusteru v části [vytvoření samostatného clusteru se systémem Windows](service-fabric-cluster-creation-for-windows-server.md).

Další informace o tom, jak zabezpečení mezi uzly, zabezpečení mezi uzly a řízení přístupu na základě rolí, najdete v tématu [scénáře zabezpečení clusteru](service-fabric-cluster-security.md).

Příklady připojení pomocí PowerShellu nebo FabricClient najdete v tématu [připojení k zabezpečenému clusteru](service-fabric-connect-to-secure-cluster.md) .
