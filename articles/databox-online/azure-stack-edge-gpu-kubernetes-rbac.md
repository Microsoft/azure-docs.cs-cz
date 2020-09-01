---
title: Pochopení Access Control založeného na rolích na zařízení Azure Stack Edge | Microsoft Docs
description: Popisuje, jak probíhá Access Control na základě rolí na Azure Stack hraničním zařízení Kubernetes.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 310fde15a850214aa1741c9cb587c0edcf570a37
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084095"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-device"></a>Kubernetes na zařízení Azure Stack Edge Access Control na základě rolí


Při konfiguraci výpočetní role na zařízení Azure Stack Edge se vytvoří cluster Kubernetes. K omezení přístupu k prostředkům clusteru v zařízení můžete použít řízení přístupu na základě role (RBAC) Kubernetes.

V tomto článku najdete přehled systému RBAC, který poskytuje Kubernetes, a o tom, jak je Kubernetes RBAC na zařízení Azure Stack Edge implementováno. 

## <a name="rbac-for-kubernetes"></a>RBAC pro Kubernetes

Kubernetes RBAC umožňuje přiřadit uživatele nebo skupiny uživatelů, oprávnění k provádění akcí, jako je vytváření nebo úpravy prostředků, nebo zobrazení protokolů ze spuštěných úloh aplikací. Tato oprávnění můžou být vymezená na jeden obor názvů nebo udělená napříč celým clusterem. 

Při nastavování clusteru Kubernetes se vytvoří jeden uživatel, který odpovídá tomuto clusteru a nazývá se uživatel správce clusteru.  K `kubeconfig` souboru je přidružen uživatel správce clusteru. `kubeconfig`Soubor je textový soubor, který obsahuje všechny konfigurační informace potřebné pro připojení ke clusteru za účelem ověření uživatele. 

### <a name="namespaces-and-users"></a>Obory názvů a uživatelé

V reálném světě je důležité rozdělit cluster na více oborů názvů. 

- **Více uživatelů**: Pokud máte více uživatelů, pak více oborů názvů umožní těmto uživatelům nasazovat své aplikace a služby v jejich specifických oborech názvů v izolaci od sebe. 
- **Jeden uživatel**: i když existuje jeden uživatel, více oborů názvů umožní, aby tento uživatel spouštěl více verzí aplikací ve stejném clusteru Kubernetes.

### <a name="roles-and-rolebindings"></a>Role a RoleBindings

Kubernetes má koncept vazby role a role, který umožňuje udělit oprávnění uživateli nebo prostředkům na úrovni oboru názvů a na úrovni clusteru. 

- **Role**: můžete definovat oprávnění pro uživatele jako **roli** a potom použít **role** k udělení oprávnění v rámci oboru názvů. 
- **RoleBindings**: Pokud jste definovali role, můžete k přiřazení rolí pro daný obor názvů použít **RoleBindings** . 

Tento přístup umožňuje logicky oddělit jeden Kubernetes cluster s uživateli, kteří mají jenom přístup k prostředkům aplikace v jejich přiřazeném oboru názvů. 


## <a name="rbac-on-azure-stack-edge"></a>RBAC na Azure Stack Edge

V aktuální implementaci RBAC Azure Stack Edge umožňuje provést následující akce z omezeného prostředí PowerShell prostředí PowerShell:

- Vytvořte obory názvů.  
- Vytvořte další uživatele.
- Udělte správci přístup k oborům názvů, které jste vytvořili. Mějte na paměti, že nemáte přístup k roli Správce clusteru ani zobrazení prostředků v rámci clusteru.
- Získejte `kubeconfig` soubor s informacemi pro přístup ke clusteru Kubernetes.


Azure Stack hraniční zařízení má několik oborů názvů System a můžete vytvořit uživatelské obory názvů se `kubeconfig` soubory pro přístup k těmto oborům názvů. Uživatelé mají plnou kontrolu nad těmito obory názvů a můžou vytvářet nebo upravovat uživatele nebo udělovat přístup uživatelům. Pouze správce clusteru má plný přístup k systémovým oborům názvů a prostředkům celého clusteru. `aseuser`Má přístup jen pro čtení k systémovým oborům názvů.

Tady je diagram, který znázorňuje implementaci RBAC na Azure Stack hraničním zařízení.

![RBAC na Azure Stack hraničním zařízení](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

V tomto diagramu mají Alice, Bob a Karel přístup pouze k přiřazeným oborům názvů uživatelů, což v tomto případě jsou `ns1` , `ns2` a `ns3` . V těchto oborech názvů mají přístup správce. Správce clusteru na druhé straně má přístup správce k systémovým oborům názvů a prostředkům v rámci clusteru.

Pomocí příkazů můžete `kubectl` vytvářet obory názvů, přiřazovat uživatele, přiřazovat uživatele nebo stahovat `kubeconfig` soubory. Tady je pracovní postup vysoké úrovně:

1. Vytvořte obor názvů a uživatele.  

    `New-HcsKubernetesNamespace -Namespace`  

2. Vytvořte uživatele.  

    `New-HcsKubernetesUser -UserName`  

3. Přidružte obor názvů k uživateli, který jste vytvořili.  

    `Grant-HcsKubernetesNamespaceAccess -Namespace -UserName`  

4. Uložte konfiguraci uživatele do `C:\Users\<username>\.kube` .  

5. Nainstalujte `kubectl` a začněte nasazovat aplikace do `kubectl` . 

Podrobné pokyny najdete [v tématu přístup ke clusteru Kubernetes prostřednictvím kuebctl na Azure Stack hraničních](azure-stack-edge-gpu-create-kubernetes-cluster.md)zařízeních.


Při práci s obory názvů a uživateli na Azure Stack hraničních zařízeních platí následující upozornění:

- Nemáte oprávnění k provádění operací, jako je vytváření uživatelů, udělení nebo odvolání přístupu k oboru názvů uživateli, pro všechny obory názvů System. Mezi příklady systémových oborů názvů patří `kube-system` , `metallb-system` , `kubernetes-dashboard` , `default` , `kube-node-lease` , `kube-public` . Obory názvů systému také zahrnují obory názvů rezervované pro typy nasazení, jako například `iotedge` obor názvů IoT Edge) a `azure-arc` (obor názvů Azure ARC).
- Můžete vytvořit uživatelské obory názvů a v rámci těchto oborů názvů vytvořit další uživatele a udělit nebo odvolat přístup k oboru názvů těmto uživatelům.
- Nemáte oprávnění vytvářet žádné obory názvů s názvy, které jsou stejné jako pro libovolný obor názvů System. Názvy pro systémové obory názvů jsou rezervované.  
- Nemůžete vytvářet žádné obory názvů uživatelů s názvy, které už používají jiné obory názvů uživatelů. Například pokud máte vytvořený, který jste `test-ns` vytvořili, nemůžete vytvořit jiný `test-ns` obor názvů.
- Nejste oprávněni vytvářet uživatele s názvy, které jsou již rezervovány. Například `aseuser` je rezervovaný Správce clusteru a nelze ho použít.

Další informace o Azure Stackch oborech názvů Edge naleznete v tématu [typy oboru názvů](azure-stack-edge-gpu-kubernetes-workload-management.md#namespaces-types).


<!--To deploy applications on an Azure Stack Edge device, use the following :
 
- First, you will use the PowerShell runspace to create a user, create a namespace, and grant user access to that namespace.
- Next, you will use the Azure Stack Edge resource in the Azure portal to create persistent volumes using either static or dynamic provisioning for the stateful applications that you will deploy.
- Finally, you will use the services to expose applications externally and within the Kubernetes cluster.-->

## <a name="next-steps"></a>Další kroky

Informace o tom, jak můžete vytvořit uživatele, vytvořit obor názvů a udělit uživatelům přístup k tomuto oboru názvů, najdete v tématu [přístup ke clusteru Kubernetes prostřednictvím kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

