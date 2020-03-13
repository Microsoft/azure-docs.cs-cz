---
title: Osvědčené postupy pro vývojáře – Pod zabezpečení ve službě Azure Kubernetes služby (AKS)
description: Další informace pro vývojáře osvědčené postupy pro zabezpečení podů ve službě Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: zarhoads
ms.openlocfilehash: eaeb81d7f93124f1f3dedf9676314b1b786d8571
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252984"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro zabezpečení pod ve službě Azure Kubernetes Service (AKS)

Vývoj a spouštění aplikací ve službě Azure Kubernetes Service (AKS), je zabezpečení pody je důležitým aspektem. Vaše aplikace by měly být navržené pro princip minimálního počtu požadovaných oprávnění. Zabezpečení dat soukromých je zřeteli pro zákazníky. Nechcete, aby se přihlašovací údaje, jako jsou databázové připojovací řetězce, klíče nebo tajné klíče a certifikáty vystaveny vnějšímu světu, kde by útočník mohl tyto tajné kódy využít ke škodlivým účelům. Nemáte přidejte do kódu nebo je vložit do imagí kontejnerů. Tento přístup by představovat riziko pro vystavení a omezit možnost obměňovat tyto přihlašovací údaje jako Image kontejneru bude třeba znovu sestavit.

Tento článek o osvědčených postupech se zaměřuje na zabezpečení lusků v AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Kontext zabezpečení pod použít k omezení přístupu k procesů a služeb nebo zvýšení úrovně oprávnění
> * Ověřování pomocí dalších prostředků Azure pomocí pod spravovaných identit
> * Vyžádání a načtení přihlašovacích údajů z digitální trezoru, jako je Azure Key Vault

Můžete si také přečíst osvědčené postupy pro [zabezpečení clusteru][best-practices-cluster-security] a [správu imagí kontejneru][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Zabezpečení pod přístupu k prostředkům

**Doprovodné materiály k osvědčeným postupům** – můžete spustit jako jiný uživatel nebo skupinu a omezit přístup k podkladovým procesům a službám uzlů, definovat pod nastavením kontextu zabezpečení. Přiřadit nejmenší počet požadovaná oprávnění.

Aby vaše aplikace běžely správně, měly by být spuštěny v rámci definovaného uživatele nebo skupiny, nikoli jako *kořen*. `securityContext` pro pod nebo kontejner umožňuje definovat nastavení, jako je například *runAsUser* nebo *fsGroup* , aby se mohla předpokládat příslušná oprávnění. Pouze požadovaného uživatele nebo skupiny oprávnění a nepoužívejte kontextu zabezpečení jako způsob předpokládat, že další oprávnění. Nastavení *runAsUser*, eskalace oprávnění a dalších možností pro Linux jsou k dispozici pouze v uzlech a luskech systému Linux.

Při spuštění jako uživatel bez kořenového kontejnery nelze vytvořit vazbu na privilegované porty v části 1 024. V tomto scénáři je možné ke skrytí skutečnost, že je aplikace spuštěna na konkrétním portu služby Kubernetes.

Kontext zabezpečení pod můžete také definovat oprávnění pro přístup k procesů a služeb nebo další funkce. Můžete nastavit následující běžné definice kontext zabezpečení:

* **allowPrivilegeEscalation** definuje, zda může rozhraním pod předpokládat *Kořenová* oprávnění. Navrhněte své aplikace, aby toto nastavení bylo vždycky nastavené na *false*.
* **Funkce systému Linux** umožňují podkladové procesy přístupu pod uzlem. Postará se přiřazení těchto možností. Přiřadit nejmenší počet potřebná oprávnění. Další informace najdete v tématu [Možnosti pro Linux][linux-capabilities].
* **SELinux Labels** je modul zabezpečení jádra pro Linux, který umožňuje definovat zásady přístupu pro služby, procesy a přístup k systému souborů. Znovu přiřadit nejmenší počet potřebná oprávnění. Další informace najdete v tématu [Možnosti SELinux v Kubernetes][selinux-labels] .

Manifest YAML pod následující příklad nastaví kontext nastavení k definování zabezpečení:

* Pod se spustí jako ID uživatele *1000* a část id skupiny *2000* .
* Nejde eskalovat oprávnění k použití `root`
* Umožňuje Linux možnosti pro přístup k síťovým rozhraním a hodiny v reálném čase (hardware) hostiteli

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

Operátor clusteru rozhodnout, jaká nastavení kontextu zabezpečení je nutné pracovat. Došlo k pokusu o navrhnout aplikace tak, že minimalizovat další oprávnění a přístupu, kterou chcete pod vyžaduje. Existují další funkce zabezpečení k omezení přístupu pomocí AppArmor a seccomp (zabezpečené výpočetní), který může být implementována operátory clusteru. Další informace najdete v tématu [zabezpečení přístupu kontejneru k prostředkům][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Limit odhalení přihlašovacích údajů

**Doprovodné materiály k osvědčeným postupům** – nedefinujte přihlašovací údaje v kódu aplikace. Pod žádost o přístup k dalším prostředkům pomocí spravované identity pro prostředky Azure. Digitální trezoru, jako je Azure Key Vault, by měla také sloužit k ukládání a načítání digitálních klíčů a přihlašovacích údajů. Spravované identity pod jsou určené pro použití jenom pro systémy Linux a image kontejnerů.

Pokud chcete omezit riziko vystavení v kódu aplikace přihlašovacích údajů, se vyhněte použití fixed nebo sdílené přihlašovací údaje. Přihlašovacím údajům nebo klíčům by neměla být obsažená přímo v kódu. Pokud tyto přihlašovací údaje jsou odhaleny, aplikace potřebuje aktualizovat a znovu nasadit. Lepším řešením je poskytnout podů vlastní identity a způsob, jak sami ověření, nebo automaticky načíst přihlašovací údaje z digitální trezoru.

Následující [přidružené open source projekty AKS][aks-associated-projects] vám umožňují automaticky ověřovat lusky nebo požadovat přihlašovací údaje a klíče z digitálního trezoru:

* Spravované identity pro prostředky Azure, a
* Azure Key Vault FlexVol ovladače

Technická podpora Azure nepodporuje přidružené open source projekty v AKS. Jsou k dispozici za účelem shromažďování názorů a chyb od naší komunity. Tyto projekty se nedoporučují pro použití v produkčním prostředí.

### <a name="use-pod-managed-identities"></a>Pod použití spravované identity

Spravovaná identita pro prostředky Azure umožňuje sám sebe ověřit vůči službám Azure, které je podporují, jako je například Storage nebo SQL. Pod se přiřadí Azure Identity, ve kterém je ověření do služby Azure Active Directory a získat digitální token. Tento digitální token lze zobrazit k jiným službám Azure, které kontrolují, jestli chcete pod autorizaci pro přístup ke službě a provádět požadované akce. Tento postup znamená, že žádné tajné klíče jsou požadovány pro databázové připojovací řetězce, například. Zjednodušený pracovní postup pro pod spravované identity můžete vidět v následujícím diagramu:

![Zjednodušený pracovní postup pro pod spravovat identity v Azure](media/developer-best-practices-pod-security/basic-pod-identity.png)

S využitím spravované identity kódu aplikace nemusí zahrnovat přihlašovací údaje pro přístup ke službě, jako je Azure Storage. Jak každý pod ověřuje pomocí vlastní identity, tak můžete auditovat a kontrolovat přístup. Pokud vaše aplikace připojuje s ostatními službami Azure, pomocí spravované identity pro opakované použití omezení přihlašovacích údajů a riziko ohrožení.

Další informace o identitách pod najdete v tématu [Konfigurace clusteru AKS, který se použije pod spravovanými identitami a s vašimi aplikacemi][aad-pod-identity] .

### <a name="use-azure-key-vault-with-flexvol"></a>Použití Azure Key Vault s FlexVol

Identity spravovaných pod ideální ověřování na základě podpůrné služby Azure. Vlastní služby nebo aplikace bez spravovaným identitám pro prostředky Azure stále ověření pomocí služby přihlašovacím údajům nebo klíčům. Digitální trezor slouží k ukládání těchto přihlašovacích údajů.

Když aplikace potřebuje přihlašovací údaje, komunikují s digitální trezor načíst nejnovější přihlašovací údaje a připojte se k požadované služby. Služba Azure Key Vault může být tento digitální trezor. Zjednodušený pracovní postup pro načtení přihlašovacích údajů služby Azure Key Vault pomocí pod spravovaných identit můžete vidět v následujícím diagramu:

![Zjednodušený pracovní postup pro načtení přihlašovacích údajů ze služby Key Vault pomocí pod spravované identity](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Se službou Key Vault ukládat a pravidelně otočit tajné kódy jako jsou přihlašovací údaje, klíče účtu úložiště nebo certifikáty. Azure Key Vault můžete integrovat s clusterem AKS pomocí FlexVolume. Ovladač FlexVolume umožňuje clusteru AKS nativně načíst přihlašovací údaje ze služby Key Vault a bezpečné poskytování přesných pouze na žádost o pod. Práce s operátorem váš cluster nasadit ovladač Key Vault FlexVol do uzlů AKS. Pod spravované identity můžete požádat o přístup do služby Key Vault a načíst přihlašovací údaje, které potřebujete prostřednictvím FlexVolume ovladače.

Azure Key Vault s FlexVol jsou určené pro použití s aplikacemi a službami běžícími v systémech Linux a uzlech.

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřuje na tom, jak zabezpečit vaše pody. K provedení některých z těchto oblastí, naleznete v následujících článcích:

* [Použití spravovaných identit pro prostředky Azure s AKS][aad-pod-identity]
* [Integrace Azure Key Vault s AKS][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo-pod
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core
[aks-associated-projects]: https://github.com/Azure/AKS/blob/master/previews.md#associated-projects

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
