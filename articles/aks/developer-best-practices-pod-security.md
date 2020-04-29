---
title: Doporučené postupy zabezpečení
titleSuffix: Azure Kubernetes Service
description: Seznamte se s osvědčenými postupy pro vývojáře při zabezpečení lusků ve službě Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: zarhoads
ms.openlocfilehash: 1f093b5276ee7ab334043e57f97a108267c32c87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804380"
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

Aby vaše aplikace běžely správně, měly by být spuštěny v rámci definovaného uživatele nebo skupiny, nikoli jako *kořen*. Pro pod nebo kontejner můžete definovat nastavení, jako je například *runAsUser* nebo fsGroup, aby se mohla předpokládat příslušná oprávnění. *fsGroup* `securityContext` Přiřaďte pouze požadovaná oprávnění uživatele nebo skupiny a nepoužívejte kontext zabezpečení jako prostředek k tomu, abyste mohli převzít další oprávnění. Nastavení *runAsUser*, eskalace oprávnění a dalších možností pro Linux jsou k dispozici pouze v uzlech a luskech systému Linux.

Když spustíte jako nerootový uživatel, kontejnery se nemůžou přivážet k privilegovaným portům pod 1024. V tomto scénáři je možné použít služby Kubernetes Services k promaskování faktu, že aplikace běží na konkrétním portu.

Kontext zabezpečení pod může také definovat další možnosti nebo oprávnění pro přístup k procesům a službám. Můžete nastavit následující definice běžných kontextů zabezpečení:

* **allowPrivilegeEscalation** definuje, zda může rozhraním pod předpokládat *Kořenová* oprávnění. Navrhněte své aplikace, aby toto nastavení bylo vždycky nastavené na *false*.
* **Funkce systému Linux** umožňují podkladové procesy přístupu pod uzlem. Pokaždé, když tyto možnosti přiřadíte, se ujistěte. Přiřaďte nejnižší počet potřebných oprávnění. Další informace najdete v tématu [Možnosti pro Linux][linux-capabilities].
* **SELinux Labels** je modul zabezpečení jádra pro Linux, který umožňuje definovat zásady přístupu pro služby, procesy a přístup k systému souborů. Znovu přiřaďte nejnižší počet potřebných oprávnění. Další informace najdete v tématu [Možnosti SELinux v Kubernetes][selinux-labels] .

Následující příklad pod YAML manifest nastaví nastavení kontextu zabezpečení k definování:

* Pod se spustí jako ID uživatele *1000* a část id skupiny *2000* .
* Nejde eskalovat oprávnění k použití.`root`
* Umožňuje systémům Linux přístup k síťovým rozhraním a hodinám v reálném čase hostitele.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  containers:
    - name: security-context-demo
      image: nginx:1.15.5
    securityContext:
      runAsUser: 1000
      fsGroup: 2000
      allowPrivilegeEscalation: false
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```

Spolupracujte se svým operátorem clusteru a určete, jaká nastavení kontextu zabezpečení potřebujete. Zkuste navrhnout své aplikace, abyste minimalizovali další oprávnění a přístup k ní vyžaduje. Existují další funkce zabezpečení, které omezují přístup pomocí AppArmor a seccomp (Secure Computing), které mohou být implementovány provozovateli clusteru. Další informace najdete v tématu [zabezpečení přístupu kontejneru k prostředkům][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Omezit expozici přihlašovacích údajů

**Doprovodné materiály k osvědčeným postupům** – nedefinujte přihlašovací údaje v kódu aplikace. Pomocí spravovaných identit pro prostředky Azure umožněte, aby váš odkaz vyžádal přístup k dalším prostředkům. K ukládání a načítání digitálních klíčů a přihlašovacích údajů by se měl použít taky digitální trezor, například Azure Key Vault. Spravované identity pod jsou určené pro použití jenom pro systémy Linux a image kontejnerů.

Chcete-li omezit riziko zpřístupnění přihlašovacích údajů v kódu aplikace, vyhněte se použití pevných nebo sdílených přihlašovacích údajů. Přihlašovací údaje nebo klíče by neměly být zahrnuty přímo do kódu. Pokud jsou tyto přihlašovací údaje vystavené, musí se aplikace aktualizovat a znovu nasadit. Lepším řešením je poskytnout v lusku svou vlastní identitu a způsob ověřování nebo automaticky načíst přihlašovací údaje z digitálního trezoru.

Následující [přidružené open source projekty AKS][aks-associated-projects] vám umožňují automaticky ověřovat lusky nebo požadovat přihlašovací údaje a klíče z digitálního trezoru:

* Spravované identity pro prostředky Azure a
* Ovladač Azure Key Vault FlexVol

Technická podpora Azure nepodporuje přidružené open source projekty v AKS. Jsou k dispozici za účelem shromažďování názorů a chyb od naší komunity. Tyto projekty se nedoporučují pro použití v produkčním prostředí.

### <a name="use-pod-managed-identities"></a>Použití pod spravovanými identitami

Spravovaná identita pro prostředky Azure umožňuje sám sebe ověřit vůči službám Azure, které je podporují, jako je například Storage nebo SQL. Pod je přiřazena identita Azure, která umožňuje ověření pro Azure Active Directory a příjem digitálního tokenu. Tento digitální token se dá předkládat ostatním službám Azure, které kontrolují, jestli má oprávnění k přístupu ke službě a provádění požadovaných akcí. Tento přístup znamená, že pro připojovací řetězce databáze nejsou vyžadovány tajné klíče. Zjednodušený pracovní postup pro spravovanou identitu pod je zobrazený v následujícím diagramu:

![Zjednodušený pracovní postup pro spravovanou identitu pod Azure](media/developer-best-practices-pod-security/basic-pod-identity.png)

Pomocí spravované identity nemusí kód vaší aplikace zahrnovat přihlašovací údaje pro přístup ke službě, jako je například Azure Storage. Každý pod se ověřuje s vlastní identitou, takže můžete auditovat a kontrolovat přístup. Pokud se vaše aplikace připojuje k jiným službám Azure, využijte spravované identity k omezení opakovaného použití přihlašovacích údajů a rizika expozice.

Další informace o identitách pod najdete v tématu [Konfigurace clusteru AKS, který se použije pod spravovanými identitami a s vašimi aplikacemi][aad-pod-identity] .

### <a name="use-azure-key-vault-with-flexvol"></a>Použití Azure Key Vault s FlexVol

Spravované identity pod fungují skvěle pro ověřování proti podpoře služeb Azure. Pro vaše vlastní služby nebo aplikace bez spravovaných identit pro prostředky Azure se i nadále ověřujete pomocí přihlašovacích údajů nebo klíčů. K uložení těchto přihlašovacích údajů se dá použít digitální trezor.

Když aplikace potřebuje přihlašovací údaje, komunikují s digitálním trezorem, načtou nejnovější přihlašovací údaje a pak se připojí k požadované službě. Azure Key Vault může být tento digitální trezor. Zjednodušený pracovní postup pro načtení přihlašovacích údajů z Azure Key Vault pomocí spravované identity se zobrazuje v následujícím diagramu:

![Zjednodušený pracovní postup pro načtení přihlašovacích údajů z Key Vault pomocí spravované identity pod](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Pomocí Key Vault ukládáte a pravidelně otáčíte tajné klíče, jako jsou přihlašovací údaje, klíče účtu úložiště nebo certifikáty. Azure Key Vault můžete integrovat s clusterem AKS pomocí FlexVolume. Ovladač FlexVolume umožňuje, aby cluster AKS nativně načítal přihlašovací údaje od Key Vault a bezpečně poskytoval pouze žadatelům pod. Pokud chcete nasadit ovladač Key Vault FlexVol do uzlů AKS, spolupracujte se svým operátorem clusteru. Pomocí spravované identity pod ní můžete požádat o přístup k Key Vault a načíst přihlašovací údaje, které potřebujete prostřednictvím ovladače FlexVolume.

Azure Key Vault s FlexVol jsou určené pro použití s aplikacemi a službami běžícími v systémech Linux a uzlech.

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřuje na zabezpečení lusků. Chcete-li implementovat některé z těchto oblastí, přečtěte si následující články:

* [Použití spravovaných identit pro prostředky Azure s AKS][aad-pod-identity]
* [Integrace Azure Key Vault s AKS][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core
[aks-associated-projects]: https://github.com/Azure/AKS/blob/master/previews.md#associated-projects

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
