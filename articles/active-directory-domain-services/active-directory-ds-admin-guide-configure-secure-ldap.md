---
title: Konfigurace zabezpečeného protokolu LDAP (LDAPS) ve službě Azure AD Domain Services | Dokumentace Microsoftu
description: Konfigurace zabezpečení protokolu LDAP (LDAPS) pro spravované domény služby Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: maheshu
ms.openlocfilehash: 22c97da35416ba1ff593dfa5e41f557ea2ab1cc0
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182242"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Konfigurace zabezpečeného protokolu LDAP (LDAPS) pro spravované domény služby Azure AD Domain Services
Tento článek popisuje, jak můžete zajistit zabezpečení Lightweight Directory Access protokolu (LDAPS) pro spravované domény služby Azure AD Domain Services. Protokol Secure LDAP se také označuje jako "Directory přístup protokolu LDAP (Lightweight) přes vrstvy SSL (Secure Sockets) / zabezpečení TLS (Transport Layer)".

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Než začnete
K provádění úkolů uvedených v tomto článku, budete potřebovat:

1. Platný **předplatného Azure**.
2. **Adresář Azure AD** – buď synchronizaci s místním adresářem nebo výhradně cloudový adresář.
3. **Azure AD Domain Services** musí být povolené pro adresář Azure AD. Pokud jste neudělali, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).
4. A **certifikátu se použije k povolení protokolu LDAPS**.

   * **Doporučené** – Získejte certifikát od důvěryhodné veřejné certifikační autority. Tato možnost konfigurace je bezpečnější.
   * Alternativně můžete taky rozhodnout [vytvořit certifikát podepsaný svým držitelem](#task-1---obtain-a-certificate-for-secure-ldap) jak uvidíte později v tomto článku.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Požadavky na certifikát protokolu secure LDAP
Získejte platný certifikát podle následujících pokynů, dříve než povolíte protokol secure LDAP. Pokud se pokusíte povolit protokol secure LDAP pro spravovanou doménu platné nebo správné certifikátem dojde k selhání.

1. **Důvěryhodného vystavitele** – certifikát musí být vystavené autoritu důvěryhodné počítače připojující se k spravované doméně pomocí protokolu secure LDAP. Tuto autoritu může být veřejná certifikační autorita (CA) nebo certifikační autorita organizace tyto počítače důvěřují.
2. **Doba života** – certifikát musí být platná pro další 3 až 6 měsíců. Když vyprší platnost certifikátu protokolu Secure LDAP pro spravovanou doménu narušení.
3. **Název subjektu** – název subjektu certifikátu musí být zástupný znak pro vaši spravovanou doménu. Například pokud název domény "contoso100.com", název subjektu certifikátu musí být "*. contoso100.com". Nastavte název DNS (alternativní název subjektu) na tento zástupný název.
4. **Použití klíče** – certifikát musí být nakonfigurované pro následující používá – digitální podpisy a šifrování klíče.
5. **Účel certifikátu** – certifikát musí být platná pro ověřování serveru SSL.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Úloha 1: získání certifikátu pro protokol secure LDAP.
První úloha zahrnuje získání certifikátu protokolu Secure LDAP pro spravovanou doménu. Máte dvě možnosti:

* Získáte certifikát od veřejné certifikační Autority nebo podnikové certifikační Autority.
* Vytvořte certifikát podepsaný svým držitelem.

> [!NOTE]
> Klientské počítače, které je potřeba připojit se k spravované doméně pomocí protokolu secure LDAP musí důvěřovat vystavitele certifikát protokolu secure LDAP.
>

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Možnost (doporučeno) - získat od certifikační autority certifikátem protokolu secure LDAP
Pokud vaše organizace získá svoje certifikáty od veřejné certifikační Autority, získáte certifikát protokolu secure LDAP od veřejné certifikační Autority. Pokud provádíte nasazení certifikační autoritu organizace, získáte certifikát protokolu secure LDAP od CA organizace.

> [!TIP]
> **Použít certifikáty podepsané svým držitelem pro spravované domény pomocí '. onmicrosoft.com "přípony domény.**
> Pokud název domény DNS vaší spravované domény končí na ". onmicrosoft.com", nelze získat certifikátem protokolu secure LDAP od veřejné certifikační autority. Protože Microsoft vlastní doménu "onmicrosoft.com", veřejné certifikační autority odmítnout problém certifikátem protokolu secure LDAP pro vás pro doménu s Tato přípona. V tomto scénáři vytvořit certifikát podepsaný svým držitelem a pomocí nich nakonfigurovat protokol secure LDAP.
>

Ujistěte se certifikát můžete získat od veřejné certifikační autority splňuje všechny požadavky uvedené v [požadavky na certifikát protokolu secure LDAP](#requirements-for-the-secure-ldap-certificate).


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Možnost B - vytvořit certifikát podepsaný svým držitelem pro protokol secure LDAP.
Pokud neočekáváte používejte certifikát od veřejné certifikační autority, můžete vytvořit certifikát podepsaný svým držitelem pro protokol secure LDAP. Vyberte tuto možnost, pokud název domény DNS vaší spravované domény končí na ". onmicrosoft.com".

**Vytvořit certifikát podepsaný svým držitelem pomocí Powershellu**

V počítači Windows otevřete nové okno Powershellu jako **správce** a zadejte následující příkazy, chcete-li vytvořit nový certifikát podepsaný svým držitelem.

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.com
```

V předchozím příkladu nahraďte "*. contoso100.com" s názvem domény DNS vaší spravované domény. For example, pokud jste vytvořili spravovanou doménu nazývá "contoso100.onmicrosoft.com", nahraďte '*. contoso100.com "v předchozím skriptu pomocí" *. contoso100.onmicrosoft.com ").

![Vyberte adresář služby Azure AD](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

Nově vytvořený certifikát podepsaný svým držitelem je umístěn v úložišti certifikátů místního počítače.


## <a name="next-step"></a>Další krok
[Úloha 2 – export certifikát protokolu secure LDAP pro. Soubor PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)
