---
title: "Konfigurace zabezpečeného LDAP (LDAPS) ve službě Azure AD Domain Services | Microsoft Docs"
description: "Konfigurace zabezpečení protokolu LDAP (LDAPS) pro spravované doméně služby Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: maheshu
ms.openlocfilehash: 771ca39b37e6fb2d75a86df3ac785bc293b4cd5f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2018
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Konfigurace zabezpečeného LDAP (LDAPS) pro spravované doméně služby Azure AD Domain Services
Tento článek ukazuje, jak můžete povolit zabezpečené Lightweight Directory přístup protokolu (LDAPS) vaší spravované domény služby Azure AD Domain Services. Zabezpečený LDAP se také označuje jako "přístup protokolu LDAP (Lightweight Directory) přes vrstvy SSL (Secure Sockets) nebo zabezpečení TLS (Transport Layer)'.

## <a name="before-you-begin"></a>Než začnete
Chcete-li provést úkoly vypsané v tomto článku, je třeba:

1. Platná **předplatné**.
2. **Adresář Azure AD** – buď synchronizovány s místní adresář nebo výhradně cloudový adresář.
3. **Azure AD Domain Services** musí být povolen pro adresář Azure AD. Pokud jste tak dosud neučinili, postupujte podle všechny úkoly popsané v [příručce Začínáme](active-directory-ds-getting-started.md).
4. A **certifikát, který se použije k povolení zabezpečeného LDAP**.

   * **Doporučená** – Získejte certifikát od důvěryhodné veřejné certifikační autority. Tato možnost konfigurace je bezpečnější.
   * Alternativně můžete taky rozhodnout k [vytvořit certifikát podepsaný svým držitelem](#task-1---obtain-a-certificate-for-secure-ldap) jak uvidíte později v tomto článku.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Požadavky pro zabezpečené certifikát protokolu LDAP
Před povolením zabezpečený LDAP, získejte platný certifikát podle následujících pokynů. Chyb narazíte, pokud se pokusíte povolit zabezpečený LDAP vaší spravované domény s neplatný nebo nesprávný certifikát.

1. **Důvěryhodného vystavitele** -certifikát musí být vydaný autoritu důvěřují počítače připojující se k spravované doméně pomocí zabezpečený LDAP. Tato autorita může být veřejné certifikační autority (CA) nebo Certifikační autoritu organizace, které tyto počítače důvěřují.
2. **Doba platnosti** -certifikát musí být platný pro další 3 až 6 měsíců. Zabezpečený LDAP přístup k vaší spravované domény dojde k narušení při vypršení platnosti certifikátu.
3. **Název subjektu** -název předmětu na certifikátu musí být zástupný znak vaší spravované domény. Například pokud název domény "contoso100.com", název subjektu certifikátu musí být: *. contoso100.com ". Nastavte název DNS (alternativní název subjektu) na tento název zástupný znak.
4. **Použití klíče** -certifikát musí být nakonfigurované pro následující používá – digitální podpisy i šifrování klíče.
5. **Účel certifikátu** -certifikát musí být platný pro ověření serveru SSL.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Úloha 1 – získání certifikátu pro zabezpečený LDAP
První úlohou zahrnuje získání certifikátu pro zabezpečený přístup protokolu LDAP k spravované doméně. Máte dvě možnosti:

* Získejte certifikát od veřejné certifikační Autority nebo podnikové certifikační Autority.
* Vytvořte certifikát podepsaný svým držitelem.

> [!NOTE]
> Klientské počítače, které je třeba se připojit k spravované doméně pomocí zabezpečený LDAP musí důvěřovat Vystavitel certifikátu zabezpečení protokolu LDAP.
>

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Možnost (doporučeno) - získat zabezpečený LDAP certifikát od certifikační autority
Pokud vaše organizace obdrží svoje certifikáty od veřejné certifikační Autority, získejte zabezpečený LDAP certifikát od veřejné certifikační Autority. Pokud nasadíte podnikové certifikační Autority, získejte zabezpečený LDAP certifikát od CA organizace.

> [!TIP]
> **Použít certifikáty podepsané svým držitelem pro spravované domény s '. onmicrosoft.com, přípony domén.**
> Pokud název domény DNS vaší spravované domény končí na ". onmicrosoft.com", nelze získat certifikát zabezpečený LDAP od veřejné certifikační autority. Microsoft vlastní doménu "onmicrosoft.com", chcete vystavit certifikát zabezpečený LDAP pro vás pro doménu s Tato přípona odmítnout veřejné certifikační autority. V tomto scénáři vytvořit certifikát podepsaný svým držitelem a který slouží ke konfiguraci zabezpečený LDAP.
>

Ujistěte se, certifikát můžete získat od veřejné certifikační autority splňuje všechny požadavky uvedené v [zabezpečený LDAP požadavky](#requirements-for-the-secure-ldap-certificate).


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Možnost B - vytvořit certifikát podepsaný svým držitelem pro zabezpečený LDAP
Pokud neočekáváte používat certifikát od veřejné certifikační autority, můžete se rozhodnout vytvořit certifikát podepsaný svým držitelem pro zabezpečený LDAP. Vyberte tuto možnost, pokud název domény DNS vaší spravované domény končí na ". onmicrosoft.com".

**Vytvořit certifikát podepsaný svým držitelem pomocí prostředí PowerShell**

Na počítači s Windows, otevřete nové okno prostředí PowerShell jako **správce** a zadejte následující příkazy, chcete-li vytvořit nový certifikát podepsaný svým držitelem.

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject *.contoso100.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.com
```

V předchozím příkladu nahraďte '*. contoso100.com "s název domény DNS vaší spravované domény. For example, pokud jste vytvořili spravované doméně nazvané 'contoso100.onmicrosoft.com', nahraďte '*. contoso100.com "v předchozím skriptu s ' *. contoso100.onmicrosoft.com').

![Vyberte adresář služby Azure AD](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

Nově vytvořený certifikát podepsaný svým držitelem se umístí do úložiště certifikátů místního počítače.


## <a name="next-step"></a>Další krok
[Úloha 2 – zabezpečený LDAP certifikát, který chcete exportovat. Soubor PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)
