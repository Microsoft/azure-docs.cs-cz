---
title: Osvědčené postupy pro vývojáře – Pod zabezpečení ve službě Azure Kubernetes služby (AKS)
description: Další informace pro vývojáře osvědčené postupy pro zabezpečení podů ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: 412f27c572953b3f44ddca54a99f75895f438f21
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53559072"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro zabezpečení pod ve službě Azure Kubernetes Service (AKS)

Vývoj a spouštění aplikací ve službě Azure Kubernetes Service (AKS), je zabezpečení pody je důležitým aspektem. Vaše aplikace by se měly navrhovat pro principu nejnižší počet požadovaná oprávnění. Zabezpečení dat soukromých je zřeteli pro zákazníky. Nechcete přihlašovací údaje, jako jsou databázové připojovací řetězce, klíče, nebo tajné kódy a certifikáty vystavené vnějšímu světu, kde může útočník využít výhod těchto tajných kódů ke škodlivým účelům. Nemáte přidejte do kódu nebo je vložit do imagí kontejnerů. Tento přístup by představovat riziko pro vystavení a omezit možnost obměňovat tyto přihlašovací údaje jako Image kontejneru bude třeba znovu sestavit.

Tento článek o osvědčených postupech se zaměřuje na zabezpečené podů ve službě AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Kontext zabezpečení pod použít k omezení přístupu k procesů a služeb nebo zvýšení úrovně oprávnění
> * Ověřování pomocí dalších prostředků Azure pomocí pod spravovaných identit
> * Vyžádání a načtení přihlašovacích údajů z digitální trezoru, jako je Azure Key Vault

Můžete si také přečíst osvědčené postupy pro [clusteru zabezpečení] [ best-practices-cluster-security] a [Správa imagí kontejnerů][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Zabezpečení pod přístupu k prostředkům

**Osvědčené postupy pro moduly** – Chcete-li spustit jako jiné uživatele nebo skupinu a omezit přístup k základní uzel procesů a služeb, nastavení zabezpečení pod kontextu. Přiřadit nejmenší počet požadovaná oprávnění.

Pro vaše aplikace správně spustit, by měl spustit podů jako definované uživatele nebo skupiny a ne jako *kořenové*. `securityContext` Pro pod nebo kontejnerů umožňuje definovat nastavení, jako *Spustit_jako_uživatel* nebo *fsGroup* předpokládat, že příslušná oprávnění. Pouze požadovaného uživatele nebo skupiny oprávnění a nepoužívejte kontextu zabezpečení jako způsob předpokládat, že další oprávnění. Při spuštění jako uživatel bez kořenového kontejnery nelze vytvořit vazbu na privilegované porty v části 1 024. V tomto scénáři je možné ke skrytí skutečnost, že je aplikace spuštěna na konkrétním portu služby Kubernetes.

Kontext zabezpečení pod můžete také definovat oprávnění pro přístup k procesů a služeb nebo další funkce. Můžete nastavit následující běžné definice kontext zabezpečení:

* **allowPrivilegeEscalation** definuje, jestli se může převzít pod *kořenové* oprávnění. Navrhnout aplikace tak, že toto nastavení je vždycky nastavený na *false*.
* **Možnosti Linux** nechat pod přístup k základní uzel procesy. Postará se přiřazení těchto možností. Přiřadit nejmenší počet potřebná oprávnění. Další informace najdete v tématu [Linux možnosti][linux-capabilities].
* **Popisky SELinux** je modul zabezpečení jádra systému Linux, který umožňuje definovat zásady přístupu pro přístup k službám, procesy a systému souborů. Znovu přiřadit nejmenší počet potřebná oprávnění. Další informace najdete v tématu [SELinux možnosti v Kubernetes][selinux-labels]

Manifest YAML pod následující příklad nastaví kontext nastavení k definování zabezpečení:

* Pod běží jako ID uživatele *1000* a ID skupiny součástí *2000*
* Nelze zvýšit oprávnění k použití `root`
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

Operátor clusteru rozhodnout, jaká nastavení kontextu zabezpečení je nutné pracovat. Došlo k pokusu o navrhnout aplikace tak, že minimalizovat další oprávnění a přístupu, kterou chcete pod vyžaduje. Existují další funkce zabezpečení k omezení přístupu pomocí AppArmor a seccomp (zabezpečené výpočetní), který může být implementována operátory clusteru. Další informace najdete v tématu [zabezpečený kontejneru přístup k prostředkům][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Limit odhalení přihlašovacích údajů

**Osvědčené postupy pro moduly** -nebudete definovat přihlašovací údaje v kódu aplikace. Pod žádost o přístup k dalším prostředkům pomocí spravované identity pro prostředky Azure. Digitální trezoru, jako je Azure Key Vault, by měla také sloužit k ukládání a načítání digitálních klíčů a přihlašovacích údajů.

Pokud chcete omezit riziko vystavení v kódu aplikace přihlašovacích údajů, se vyhněte použití fixed nebo sdílené přihlašovací údaje. Přihlašovacím údajům nebo klíčům by neměla být obsažená přímo v kódu. Pokud tyto přihlašovací údaje jsou odhaleny, aplikace potřebuje aktualizovat a znovu nasadit. Lepším řešením je poskytnout podů vlastní identity a způsob, jak sami ověření, nebo automaticky načíst přihlašovací údaje z digitální trezoru.

AKS zahrnuje dva způsoby, jak automaticky ověřovat podů nebo žádost o přihlašovací údaje a klíče z trezoru digitální:

* Spravované identity pro prostředky Azure, a
* Azure Key Vault FlexVol ovladače

### <a name="use-pod-managed-identities"></a>Pod použití spravované identity

Spravovaná identita pro prostředky Azure umožňuje pod samotné ověření používat jakoukoliv službu v Azure, která podporuje jako jsou Storage, SQL. Pod se přiřadí Azure Identity, ve kterém je ověření do služby Azure Active Directory a získat digitální token. Tento digitální token lze zobrazit k jiným službám Azure, které kontrolují, jestli chcete pod autorizaci pro přístup ke službě a provádět požadované akce. Tento postup znamená, že žádné tajné klíče jsou požadovány pro databázové připojovací řetězce, například. Zjednodušený pracovní postup pro pod spravované identity můžete vidět v následujícím diagramu:

![Zjednodušený pracovní postup pro pod spravovat identity v Azure](media/developer-best-practices-pod-security/basic-pod-identity.png)

S využitím spravované identity kódu aplikace nemusí zahrnovat přihlašovací údaje pro přístup ke službě, jako je Azure Storage. Jak každý pod ověřuje pomocí vlastní identity, tak můžete auditovat a kontrolovat přístup. Pokud vaše aplikace připojuje s ostatními službami Azure, pomocí spravované identity pro opakované použití omezení přihlašovacích údajů a riziko ohrožení.

Další informace o identitách pod najdete v tématu [konfigurace clusteru AKS používat pod spravované identity] [ aad-pod-identity] a [přiřadit a používat pod spravované identity ve vašem kódu] [ aad-pod-identity].

### <a name="use-azure-key-vault-with-flexvol"></a>Použití Azure Key Vault s FlexVol

Identity spravovaných pod ideální ověřování na základě podpůrné služby Azure. Vlastní služby nebo aplikace bez spravovaným identitám pro prostředky Azure stále ověření pomocí služby přihlašovacím údajům nebo klíčům. Digitální trezor slouží k ukládání těchto přihlašovacích údajů.

Když aplikace potřebuje přihlašovací údaje, komunikují s digitální trezor načíst nejnovější přihlašovací údaje a připojte se k požadované služby. Služba Azure Key Vault může být tento digitální trezor. Zjednodušený pracovní postup pro načtení přihlašovacích údajů služby Azure Key Vault pomocí pod spravovaných identit můžete vidět v následujícím diagramu:

![Zjednodušený pracovní postup pro načtení přihlašovacích údajů ze služby Key Vault pomocí pod spravované identity](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Se službou Key Vault ukládat a pravidelně otočit tajné kódy jako jsou přihlašovací údaje, klíče účtu úložiště nebo certifikáty. Azure Key Vault můžete integrovat s clusterem AKS pomocí FlexVolume. Ovladač FlexVolume umožňuje clusteru AKS nativně načíst přihlašovací údaje ze služby Key Vault a bezpečné poskytování přesných pouze na žádost o pod. Práce s operátorem váš cluster nasadit ovladač Key Vault FlexVol do uzlů AKS. Pod spravované identity můžete požádat o přístup do služby Key Vault a načíst přihlašovací údaje, které potřebujete prostřednictvím FlexVolume ovladače.

## <a name="next-steps"></a>Další postup

Tento článek se zaměřuje na tom, jak zabezpečit vaše pody. K provedení některých z těchto oblastí, naleznete v následujících článcích:

* [Použití spravované identity pro prostředky Azure s AKS][aad-pod-identity]
* [Integrace Azure Key Vault s AKS][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo-pod
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
