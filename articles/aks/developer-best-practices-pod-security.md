---
title: Osvědčené postupy pro vývojáře – pod zabezpečením ve službě Azure Kubernetes Services (AKS)
description: Seznamte se s osvědčenými postupy pro vývojáře při zabezpečení lusků ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: zarhoads
ms.openlocfilehash: 1c7143b6d3479cf3083cfc730301c68dcf4eb705
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900812"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro zabezpečení pod zabezpečením ve službě Azure Kubernetes Service (AKS)

Při vývoji a spouštění aplikací ve službě Azure Kubernetes Service (AKS) je nejdůležitějším aspektem zabezpečení lusků. Vaše aplikace by měly být navržené pro princip minimálního počtu požadovaných oprávnění. Zabezpečení privátních dat je pro zákazníky nejdůležitější. Nechcete, aby se přihlašovací údaje, jako jsou databázové připojovací řetězce, klíče nebo tajné klíče a certifikáty vystaveny vnějšímu světu, kde by útočník mohl tyto tajné kódy využít ke škodlivým účelům. Nepřidávejte je do kódu nebo je vložte do svých imagí kontejneru. Tento přístup by vytvořil riziko pro vystavení a omezení schopnosti tyto přihlašovací údaje otočit, protože image kontejneru se musí znovu sestavit.

Tento článek o osvědčených postupech se zaměřuje na zabezpečení lusků v AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Použijte kontext zabezpečení pod, pokud chcete omezit přístup k procesům a službám nebo eskalaci oprávnění.
> * Ověřování s ostatními prostředky Azure pomocí spravovaných identit pod
> * Vyžádejte a načtěte přihlašovací údaje z digitálního trezoru, jako je Azure Key Vault

Můžete si také přečíst osvědčené postupy pro [zabezpečení clusteru][best-practices-cluster-security] a [správu imagí kontejneru][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Zabezpečení pod přístupem k prostředkům

**Doprovodné materiály k osvědčeným postupům** – můžete spustit jako jiný uživatel nebo skupinu a omezit přístup k podkladovým procesům a službám uzlů, definovat pod nastavením kontextu zabezpečení. Přiřaďte minimální počet požadovaných oprávnění.

Aby vaše aplikace běžely správně, měly by být spuštěny v rámci definovaného uživatele nebo skupiny, nikoli jako *kořen* . `securityContext`Pro pod nebo kontejner můžete definovat nastavení, jako je například *RunAsUser* nebo *fsGroup* , aby se mohla předpokládat příslušná oprávnění. Přiřaďte pouze požadovaná oprávnění uživatele nebo skupiny a nepoužívejte kontext zabezpečení jako prostředek k tomu, abyste mohli převzít další oprávnění. Nastavení *runAsUser* , eskalace oprávnění a dalších možností pro Linux jsou k dispozici pouze v uzlech a luskech systému Linux.

Když spustíte jako nerootový uživatel, kontejnery se nemůžou přivážet k privilegovaným portům pod 1024. V tomto scénáři je možné použít služby Kubernetes Services k promaskování faktu, že aplikace běží na konkrétním portu.

Kontext zabezpečení pod může také definovat další možnosti nebo oprávnění pro přístup k procesům a službám. Můžete nastavit následující definice běžných kontextů zabezpečení:

* **allowPrivilegeEscalation** definuje, zda může rozhraním pod předpokládat *Kořenová* oprávnění. Navrhněte své aplikace, aby toto nastavení bylo vždycky nastavené na *false* .
* **Funkce systému Linux** umožňují podkladové procesy přístupu pod uzlem. Pokaždé, když tyto možnosti přiřadíte, se ujistěte. Přiřaďte nejnižší počet potřebných oprávnění. Další informace najdete v tématu [Možnosti pro Linux][linux-capabilities].
* **SELinux Labels** je modul zabezpečení jádra pro Linux, který umožňuje definovat zásady přístupu pro služby, procesy a přístup k systému souborů. Znovu přiřaďte nejnižší počet potřebných oprávnění. Další informace najdete v tématu [Možnosti SELinux v Kubernetes][selinux-labels] .

Následující příklad pod YAML manifest nastaví nastavení kontextu zabezpečení k definování:

* Pod se spustí jako ID uživatele *1000* a část id skupiny *2000* .
* Nejde eskalovat oprávnění k použití. `root`
* Umožňuje systémům Linux přístup k síťovým rozhraním a hodinám v reálném čase hostitele.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    fsGroup: 2000
  containers:
    - name: security-context-demo
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        runAsUser: 1000
        allowPrivilegeEscalation: false
        capabilities:
          add: ["NET_ADMIN", "SYS_TIME"]
```

Spolupracujte se svým operátorem clusteru a určete, jaká nastavení kontextu zabezpečení potřebujete. Zkuste navrhnout své aplikace, abyste minimalizovali další oprávnění a přístup k ní vyžaduje. Existují další funkce zabezpečení, které omezují přístup pomocí AppArmor a seccomp (Secure Computing), které mohou být implementovány provozovateli clusteru. Další informace najdete v tématu [zabezpečení přístupu kontejneru k prostředkům][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Omezit expozici přihlašovacích údajů

**Doprovodné materiály k osvědčeným postupům** – nedefinujte přihlašovací údaje v kódu aplikace. Pomocí spravovaných identit pro prostředky Azure umožněte, aby váš odkaz vyžádal přístup k dalším prostředkům. K ukládání a načítání digitálních klíčů a přihlašovacích údajů by se měl použít taky digitální trezor, například Azure Key Vault. Spravované identity pod jsou určené pro použití jenom pro systémy Linux a image kontejnerů.

Chcete-li omezit riziko zpřístupnění přihlašovacích údajů v kódu aplikace, vyhněte se použití pevných nebo sdílených přihlašovacích údajů. Přihlašovací údaje nebo klíče by neměly být zahrnuty přímo do kódu. Pokud jsou tyto přihlašovací údaje vystavené, musí se aplikace aktualizovat a znovu nasadit. Lepším řešením je poskytnout v lusku svou vlastní identitu a způsob ověřování nebo automaticky načíst přihlašovací údaje z digitálního trezoru.

### <a name="use-azure-container-compute-upstream-projects"></a>Použití nadřazeného projektu Azure Container COMPUTE

> [!IMPORTANT]
> Technická podpora Azure nepodporuje přidružené open source projekty v AKS. Jsou k dispozici uživatelům, kteří si můžou do clusterů sami instalovat a shromažďovat názory z naší komunity.

Následující [přidružené open source projekty AKS][aks-associated-projects] vám umožňují automaticky ověřovat lusky nebo požadovat přihlašovací údaje a klíče z digitálního trezoru. Tyto projekty se udržují v nadřazeném týmu Azure Container COMPUTE a jsou součástí [širšího seznamu projektů, které je možné použít](https://github.com/Azure/container-compute-upstream/blob/master/README.md#support).

 * [Identita Azure Active Directory pod][aad-pod-identity]
 * [Ovladač Azure Key Vault pro úložiště tajných klíčů](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage)

#### <a name="use-pod-managed-identities"></a>Použití pod spravovanými identitami

Spravovaná identita pro prostředky Azure umožňuje sám sebe ověřit vůči službám Azure, které je podporují, jako je například Storage nebo SQL. Pod je přiřazena identita Azure, která umožňuje ověření pro Azure Active Directory a příjem digitálního tokenu. Tento digitální token se dá předkládat ostatním službám Azure, které kontrolují, jestli má oprávnění k přístupu ke službě a provádění požadovaných akcí. Tento přístup znamená, že pro připojovací řetězce databáze nejsou vyžadovány tajné klíče. Zjednodušený pracovní postup pro spravovanou identitu pod je zobrazený v následujícím diagramu:

:::image type="content" source="media/developer-best-practices-pod-security/basic-pod-identity.svg" alt-text="Zjednodušený pracovní postup pro spravovanou identitu pod Azure":::

Pomocí spravované identity nemusí kód vaší aplikace zahrnovat přihlašovací údaje pro přístup ke službě, jako je například Azure Storage. Každý pod se ověřuje s vlastní identitou, takže můžete auditovat a kontrolovat přístup. Pokud se vaše aplikace připojuje k jiným službám Azure, využijte spravované identity k omezení opakovaného použití přihlašovacích údajů a rizika expozice.

Další informace o identitách pod najdete v tématu [Konfigurace clusteru AKS, který se použije pod spravovanými identitami a s vašimi aplikacemi][aad-pod-identity] .

#### <a name="use-azure-key-vault-with-secrets-store-csi-driver"></a>Použití Azure Key Vault s úložištěm tajných kódů – ovladač CSI

Použití projektu identity pod umožňuje ověřování v rámci podpory služeb Azure. Pro vaše vlastní služby nebo aplikace bez spravovaných identit pro prostředky Azure se můžete i nadále ověřovat pomocí přihlašovacích údajů nebo klíčů. K uložení obsahu tajného klíče se dá použít digitální trezor.

Když aplikace potřebuje přihlašovací údaje, komunikují s digitálním trezorem, načtou nejnovější tajný obsah a pak se připojí k požadované službě. Azure Key Vault může být tento digitální trezor. Zjednodušený pracovní postup pro načtení přihlašovacích údajů z Azure Key Vault pomocí spravované identity se zobrazuje v následujícím diagramu:

:::image type="content" source="media/developer-best-practices-pod-security/basic-key-vault.svg" alt-text="Zjednodušený pracovní postup pro spravovanou identitu pod Azure":::

Pomocí Key Vault ukládáte a pravidelně otáčíte tajné klíče, jako jsou přihlašovací údaje, klíče účtu úložiště nebo certifikáty. Azure Key Vault můžete integrovat s clusterem AKS pomocí [zprostředkovatele Azure Key Vault pro ovladač pro úložiště tajných klíčů](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage). Ovladač CSI úložiště tajných kódů umožňuje, aby cluster AKS nativně načetl obsah tajných kódů z Key Vault a bezpečně poskytoval pouze žadatelům nacházející se pod ním. Spolupracujte se svým operátorem clusteru, abyste nasadili ovladač do AKS pracovních uzlů pro úložiště tajných klíčů. Pomocí spravované identity pod ní můžete požádat o přístup k Key Vault a načíst obsah v tajnosti potřebný prostřednictvím ovladače v úložišti tajných kódů.

Azure Key Vault s úložištěm tajných kódů – ovladač CSI se dá použít pro uzly Linux a lusky, které vyžadují verzi Kubernetes 1,16 nebo vyšší. Pro uzly Windows a lusky je vyžadována verze Kubernetes 1,18 nebo vyšší.

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřuje na zabezpečení lusků. Chcete-li implementovat některé z těchto oblastí, přečtěte si následující články:

* [Použití spravovaných identit pro prostředky Azure s AKS][aad-pod-identity]
* [Integrace Azure Key Vault s AKS][aks-keyvault-csi-driver]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo
[aks-keyvault-csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#selinuxoptions-v1-core
[aks-associated-projects]: https://awesomeopensource.com/projects/aks?categoryPage=11

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
