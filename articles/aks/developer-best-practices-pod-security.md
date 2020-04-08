---
title: Doporučené postupy zabezpečení podu
titleSuffix: Azure Kubernetes Service
description: Seznamte se s doporučenými postupy pro vývojáře, jak zabezpečit pody ve službě Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: zarhoads
ms.openlocfilehash: 1f093b5276ee7ab334043e57f97a108267c32c87
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804380"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Doporučené postupy pro zabezpečení podu ve službě Azure Kubernetes Service (AKS)

Při vývoji a spouštění aplikací ve službě Azure Kubernetes Service (AKS) je zabezpečení podů klíčovým aspektem. Vaše aplikace by měly být navrženy pro zásadu nejmenšího počtu požadovaných oprávnění. Zabezpečení soukromých dat je pro zákazníky na prvním místě. Nechcete, aby pověření, jako jsou připojovací řetězce databáze, klíče nebo tajné klíče a certifikáty vystavené vnějšímu světu, kde by útočník mohl tyto tajné klíče využít ke škodlivým účelům. Nepřidávejte je do kódu ani je nevkládejte do iniciovaných kontejnerů. Tento přístup by vytvořil riziko pro expozici a omezil možnost otočení těchto přihlašovacích údajů, protože image kontejneru bude nutné znovu sestavit.

Tento článek osvědčených postupů se zaměřuje na zabezpečení podů v AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Použití kontextu zabezpečení podu k omezení přístupu k procesům a službám nebo eskalaci oprávnění
> * Ověření pomocí jiných prostředků Azure pomocí spravovaných identit podu
> * Vyžádání a načtení přihlašovacích údajů z digitálního trezoru, jako je azure key vault

Můžete si také přečíst osvědčené postupy pro [zabezpečení clusteru][best-practices-cluster-security] a [pro správu image kontejneru][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Zabezpečený přístup podu k prostředkům

**Pokyny pro osvědčené postupy** – Chcete-li spustit jako jiný uživatel nebo skupinu a omezit přístup k základním procesům a službám uzlů, definujte nastavení kontextu zabezpečení podu. Přiřaďte nejmenší počet požadovaných oprávnění.

Aby vaše aplikace běžely správně, pody by měly běžet jako definovaný uživatel nebo skupina a ne jako *root*. For `securityContext` pro pod nebo kontejner umožňuje definovat nastavení, jako je *runAsUser* nebo *fsGroup* převzít příslušná oprávnění. Přiřazuji pouze požadovaná oprávnění uživatele nebo skupiny a nepoužívejte kontext zabezpečení jako prostředek k převzetí dalších oprávnění. *RunAsUser*, eskalace oprávnění a další nastavení funkcí Linuxu jsou k dispozici pouze na linuxových uzlech a podech.

Při spuštění jako uživatel bez kořenů kontejnery nelze vytvořit vazbu na privilegované porty pod 1024. V tomto scénáři Kubernetes služby lze zamaskovat skutečnost, že aplikace běží na konkrétním portu.

Kontext zabezpečení podu můžete také definovat další možnosti nebo oprávnění pro přístup k procesům a službám. Lze nastavit následující společné definice kontextu zabezpečení:

* **allowPrivilegeEscalation** definuje, zda pod může převzít *oprávnění kořenového adresáře.* Navrhněte aplikace tak, aby toto nastavení bylo vždy nastaveno na *hodnotu false*.
* **Linux schopnosti** umožňují pod přístup základní procesy uzlu. Při přiřazování těchto funkcí dbát e-li. Přiřaďte nejmenší počet potřebných oprávnění. Další informace naleznete v tématu [Linux capabilities][linux-capabilities].
* **SELinux štítky** je linuxové jádro bezpečnostní modul, který vám umožní definovat přístupové politiky pro služby, procesy a přístup k souborové soustavě. Opět přiřaďte nejmenší počet potřebných oprávnění. Další informace naleznete [v tématu MOŽNOSTI SELinuxu v Kubernetes][selinux-labels]

Následující příklad pod manifestem YAML nastaví nastavení kontextu zabezpečení tak, aby definovala:

* Pod běží jako ID uživatele *1000* a část ID skupiny *2000*
* Nelze eskalovat oprávnění k použití`root`
* Umožňuje linuxovým funkcím přístup k síťovým rozhraním a hodinám v reálném čase (hardwaru) hostitele

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

Spolupracujte s operátorem clusteru a zjistěte, jaké nastavení kontextu zabezpečení potřebujete. Pokuste se navrhnout aplikace minimalizovat další oprávnění a přístup pod vyžaduje. Existují další funkce zabezpečení pro omezení přístupu pomocí AppArmor a seccomp (zabezpečené výpočetní techniky), které mohou být implementovány operátory clusteru. Další informace naleznete v [tématu Zabezpečený přístup kontejneru k prostředkům][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Omezit expozici pověření

**Pokyny pro osvědčené postupy** – nedefinujte pověření v kódu aplikace. Pomocí spravovaných identit pro prostředky Azure umožníte podu požadovat přístup k jiným prostředkům. Digitální trezor, jako je například Azure Key Vault, by měl být také použit k ukládání a načítání digitálních klíčů a přihlašovacích údajů. Spravované identity podů jsou určeny pouze pro linuxové pody a image kontejnerů.

Chcete-li omezit riziko vystavení pověření v kódu aplikace, vyhněte se použití pevných nebo sdílených pověření. Přihlašovací údaje nebo klíče by neměly být zahrnuty přímo do vašeho kódu. Pokud jsou tato pověření vystavena, je třeba aktualizovat a znovu nasadit aplikaci. Lepším přístupem je poskytnout podům vlastní identitu a způsob, jak se ověřit, nebo automaticky načíst přihlašovací údaje z digitálního trezoru.

Následující [přidružené projekty s otevřeným zdrojovým kódem AKS][aks-associated-projects] umožňují automaticky ověřovat pody nebo požadovat pověření a klíče z digitálního trezoru:

* Spravované identity pro prostředky Azure a
* Ovladač FlexVol trezoru klíčů Azure

Přidružené projekty s otevřeným zdrojovým kódem AKS nejsou podporovány technickou podporou Azure. Jsou poskytovány shromažďovat zpětnou vazbu a chyby z naší komunity. Tyto projekty se nedoporučují pro použití ve výrobě.

### <a name="use-pod-managed-identities"></a>Použití spravovaných identit podu

Spravovaná identita pro prostředky Azure umožňuje podu ověřit sám proti službám Azure, které ji podporují, jako je úložiště nebo SQL. Pod je přiřazen a Azure Identity, která jim umožní ověřit azure active directory a přijímat digitální token. Tento digitální token lze prezentovat jiným službám Azure, které zkontrolují, jestli je pod oprávněný k přístupu ke službě a k provedení požadovaných akcí. Tento přístup znamená, že žádné tajné klíče jsou vyžadovány pro připojovací řetězce databáze, například. Zjednodušený pracovní postup pro identitu spravované podem je uveden v následujícím diagramu:

![Zjednodušený pracovní postup pro spravovanou identitu podu v Azure](media/developer-best-practices-pod-security/basic-pod-identity.png)

Se spravovanou identitou nemusí kód aplikace obsahovat přihlašovací údaje pro přístup ke službě, jako je Azure Storage. Jako každý pod ověřuje s vlastní identitou, takže můžete auditovat a kontrolovat přístup. Pokud se vaše aplikace připojuje k jiným službám Azure, použijte spravované identity k omezení opakovaného použití přihlašovacích údajů a rizika expozice.

Další informace o identitách podu [najdete v tématu Konfigurace clusteru AKS pro použití spravovaných identit podů a s vašimi aplikacemi.][aad-pod-identity]

### <a name="use-azure-key-vault-with-flexvol"></a>Použití trezoru klíčů Azure s FlexVol

Spravované identity podu fungují skvěle ověřit proti podpoře služeb Azure. U vlastních služeb nebo aplikací bez spravovaných identit pro prostředky Azure se stále ověřujete pomocí přihlašovacích údajů nebo klíčů. K uložení těchto přihlašovacích údajů lze použít digitální trezor.

Když aplikace potřebují pověření, komunikují s digitálním trezorem, načítají nejnovější pověření a pak se připojují k požadované službě. Azure Key Vault může být tento digitální trezor. Zjednodušený pracovní postup pro načítání přihlašovacích údajů z azure key vault pomocí pod spravované identity se zobrazí v následujícím diagramu:

![Zjednodušený pracovní postup pro načítání přihlašovacích údajů z trezoru klíčů pomocí spravované identity podu](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Pomocí trezoru klíčů ukládáte a pravidelně střídáte tajné klíče, jako jsou přihlašovací údaje, klíče účtů úložiště nebo certifikáty. Azure Key Vault můžete integrovat s clusterem AKS pomocí FlexVolume. Ovladač FlexVolume umožňuje clusteru AKS nativně načíst pověření z trezoru klíčů a bezpečně je poskytnout pouze žádajícímu podu. Spolupracujte s operátorem clusteru na nasazení ovladače FlexVol trezoru klíčů do uzlů AKS. Pomocí spravované identity podu můžete požádat o přístup k trezoru klíčů a načíst potřebná pověření prostřednictvím ovladače FlexVolume.

Azure Key Vault s FlexVol je určen pro použití s aplikacemi a službami běžícími na podech a uzlech Linuxu.

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřil na to, jak zabezpečit vaše lusky. Chcete-li implementovat některé z těchto oblastí, naleznete v následujících článcích:

* [Použití spravovaných identit pro prostředky Azure pomocí AKS][aad-pod-identity]
* [Integrace trezoru klíčů Azure s AKS][aks-keyvault-flexvol]

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
