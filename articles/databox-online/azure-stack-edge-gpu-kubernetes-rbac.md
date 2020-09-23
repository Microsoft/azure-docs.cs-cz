---
title: Pochopení Access Control založeného na rolích na zařízení Azure Stack Edge pro Kubernetes | Microsoft Docs
description: Popisuje, jak se Access Control na základě role na zařízení Azure Stack Edge pro Kubernetes.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 1f194424a4030a2b829af6c8f5b97a3c200bd2e6
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90899286"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-pro-gpu-device"></a>Kubernetes Access Control na základě rolí na zařízení GPU pro Azure Stack Edge pro


Když nakonfigurujete výpočetní roli na zařízení Azure Stack Edge pro, vytvoří se cluster Kubernetes. K omezení přístupu k prostředkům clusteru v zařízení můžete použít řízení přístupu na základě role (RBAC) Kubernetes.

Tento článek poskytuje přehled o systému RBAC, který poskytuje Kubernetes, a o tom, jak je Kubernetes RBAC implementováno na zařízení Azure Stack Edge pro. 

## <a name="rbac-for-kubernetes"></a>RBAC pro Kubernetes

Kubernetes RBAC umožňuje přiřadit uživatele nebo skupiny uživatelů, oprávnění k provádění akcí, jako je vytváření nebo úpravy prostředků, nebo zobrazení protokolů ze spuštěných úloh aplikací. Tato oprávnění můžou být vymezená na jeden obor názvů nebo udělená napříč celým clusterem. 

Při nastavování clusteru Kubernetes se vytvoří jeden uživatel, který odpovídá tomuto clusteru a nazývá se uživatel správce clusteru.  K `kubeconfig` souboru je přidružen uživatel správce clusteru. `kubeconfig`Soubor je textový soubor, který obsahuje všechny konfigurační informace potřebné pro připojení ke clusteru za účelem ověření uživatele.

## <a name="namespaces-types"></a>Typy oborů názvů

Prostředky Kubernetes, například lusky a nasazení, jsou logicky seskupeny do oboru názvů. Tato seskupení poskytují způsob, jak logicky rozdělit cluster Kubernetes a omezit přístup k vytváření, zobrazení nebo správě prostředků. Uživatelé můžou pracovat jenom s prostředky v rámci svých přiřazených oborů názvů.

Obory názvů jsou určené pro použití v prostředích s mnoha uživateli, kteří jsou rozloženi mezi několik týmů nebo projektů. Další informace najdete v tématu [obory názvů Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).

Vaše zařízení Azure Stack Edge pro má následující obory názvů:

- **Obor názvů System** – tento obor názvů tam, kde existují základní prostředky, jako jsou například síťové funkce jako DNS a proxy nebo řídicí panel Kubernetes. Do tohoto oboru názvů obvykle nesadíte vlastní aplikace. Tento obor názvů použijte k ladění všech problémů clusteru Kubernetes. 

    V zařízení existuje několik oborů názvů systému a názvy odpovídající těmto oborům názvů systému jsou rezervované. Tady je seznam rezervovaných oborů názvů systému: 
    - Kube – systém
    - metallb – systém
    - DBE – obor názvů
    - default
    - Kubernetes – řídicí panel
    - Kube-Node-zapůjčení
    - Kube – veřejný


    Ujistěte se, že nepoužíváte žádné rezervované názvy pro uživatelské obory názvů, které vytvoříte. 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **Obor názvů uživatele** – jedná se o obory názvů, které můžete vytvořit prostřednictvím **kubectl** nebo prostřednictvím rozhraní PowerShellu zařízení pro místní nasazení aplikací.
 
- **Obor názvů IoT Edge** – k tomuto oboru názvů se připojíte `iotedge` , abyste mohli spravovat aplikace nasazené prostřednictvím IoT Edge.

- **Obor názvů ARC Azure** – k tomuto oboru názvů se připojíte `azure-arc` , abyste mohli spravovat aplikace nasazené přes Azure ARC. Pomocí ARC Azure můžete nasazovat i aplikace v jiných oborech názvů uživatelů. 

## <a name="namespaces-and-users"></a>Obory názvů a uživatelé

V reálném světě je důležité rozdělit cluster na více oborů názvů. 

- **Více uživatelů**: Pokud máte více uživatelů, pak více oborů názvů umožní těmto uživatelům nasazovat své aplikace a služby v jejich specifických oborech názvů v izolaci od sebe. 
- **Jeden uživatel**: i když existuje jeden uživatel, více oborů názvů umožní, aby tento uživatel spouštěl více verzí aplikací ve stejném clusteru Kubernetes.

### <a name="roles-and-rolebindings"></a>Role a RoleBindings

Kubernetes má koncept vazby role a role, který umožňuje udělit oprávnění uživateli nebo prostředkům na úrovni oboru názvů a na úrovni clusteru. 

- **Role**: můžete definovat oprávnění pro uživatele jako **roli** a potom použít **role** k udělení oprávnění v rámci oboru názvů. 
- **RoleBindings**: Pokud jste definovali role, můžete k přiřazení rolí pro daný obor názvů použít **RoleBindings** . 

Tento přístup umožňuje logicky oddělit jeden Kubernetes cluster s uživateli, kteří mají jenom přístup k prostředkům aplikace v jejich přiřazeném oboru názvů. 

## <a name="rbac-on-azure-stack-edge-pro"></a>RBAC na Azure Stack Edge pro

V aktuální implementaci RBAC Azure Stack Edge pro umožňuje provést následující akce z prostředí PowerShell s omezeným přístupem:

- Vytvořte obory názvů.  
- Vytvořte další uživatele.
- Udělte správci přístup k oborům názvů, které jste vytvořili. Mějte na paměti, že nemáte přístup k roli Správce clusteru ani zobrazení prostředků v rámci clusteru.
- Získejte `kubeconfig` soubor s informacemi pro přístup ke clusteru Kubernetes.


Zařízení Azure Stack Edge pro má několik oborů názvů System a můžete vytvořit uživatelské obory názvů se `kubeconfig` soubory pro přístup k těmto oborům názvů. Uživatelé mají plnou kontrolu nad těmito obory názvů a můžou vytvářet nebo upravovat uživatele nebo udělovat přístup uživatelům. Pouze správce clusteru má plný přístup k systémovým oborům názvů a prostředkům celého clusteru. `aseuser`Má přístup jen pro čtení k systémovým oborům názvů.

Tady je diagram, který znázorňuje implementaci RBAC na zařízeních Azure Stack Edge pro.

![RBAC na zařízení Azure Stack Edge pro](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

V tomto diagramu mají Alice, Bob a Karel přístup pouze k přiřazeným oborům názvů uživatelů, což v tomto případě jsou `ns1` , `ns2` a `ns3` . V těchto oborech názvů mají přístup správce. Správce clusteru na druhé straně má přístup správce k systémovým oborům názvů a prostředkům v rámci clusteru.

Pomocí příkazů můžete `kubectl` vytvářet obory názvů a uživatele, přiřazovat uživatele k oborům názvů nebo stahovat `kubeconfig` soubory. Tady je pracovní postup vysoké úrovně:

1. Vytvořte obor názvů a uživatele.  

    `New-HcsKubernetesNamespace -Namespace`  

2. Vytvořte uživatele.  

    `New-HcsKubernetesUser -UserName`  

3. Přidružte obor názvů k uživateli, který jste vytvořili.  

    `Grant-HcsKubernetesNamespaceAccess -Namespace -UserName`  

4. Uložte konfiguraci uživatele do `C:\Users\<username>\.kube` .  

5. Nainstalujte `kubectl` a začněte nasazovat aplikace do `kubectl` . 

Podrobné pokyny najdete [v tématu přístup ke clusteru Kubernetes prostřednictvím kuebctl na Azure Stack Edge pro](azure-stack-edge-gpu-create-kubernetes-cluster.md).


Při práci s obory názvů a uživateli na zařízeních Azure Stack Edge pro platí následující upozornění:

- Nemáte oprávnění k provádění operací, jako je vytváření uživatelů, udělení nebo odvolání přístupu k oboru názvů uživateli, pro všechny obory názvů System. Mezi příklady systémových oborů názvů patří `kube-system` , `metallb-system` , `kubernetes-dashboard` , `default` , `kube-node-lease` , `kube-public` . Obory názvů systému také zahrnují obory názvů rezervované pro typy nasazení, jako například `iotedge` obor názvů IoT Edge) a `azure-arc` (obor názvů Azure ARC).
- Můžete vytvořit uživatelské obory názvů a v rámci těchto oborů názvů vytvořit další uživatele a udělit nebo odvolat přístup k oboru názvů těmto uživatelům.
- Nemáte oprávnění vytvářet žádné obory názvů s názvy, které jsou stejné jako pro libovolný obor názvů System. Názvy pro systémové obory názvů jsou rezervované.  
- Nemůžete vytvářet žádné obory názvů uživatelů s názvy, které už používají jiné obory názvů uživatelů. Například pokud máte vytvořený, který jste `test-ns` vytvořili, nemůžete vytvořit jiný `test-ns` obor názvů.
- Nejste oprávněni vytvářet uživatele s názvy, které jsou již rezervovány. Například `aseuser` je rezervovaný uživatel a nelze jej použít.


## <a name="next-steps"></a>Další kroky

Informace o tom, jak můžete vytvořit uživatele, vytvořit obor názvů a udělit uživatelům přístup k tomuto oboru názvů, najdete v tématu [přístup ke clusteru Kubernetes prostřednictvím kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

