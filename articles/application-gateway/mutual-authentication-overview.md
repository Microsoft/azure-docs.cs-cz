---
title: Přehled vzájemného ověřování v Azure Application Gateway
description: Tento článek představuje přehled vzájemného ověřování na Application Gateway.
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.date: 03/30/2021
ms.topic: conceptual
ms.author: caya
ms.openlocfilehash: a63697c2c9c32dfb48e77248a5e7a601677b8b3e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231397"
---
# <a name="overview-of-mutual-authentication-with-application-gateway-preview"></a>Přehled vzájemného ověřování pomocí Application Gateway (Preview)

Vzájemné ověřování nebo ověřování klientů umožňuje Application Gateway ověřovat požadavky klientů na odeslání požadavků. Obvykle pouze klient ověřuje Application Gateway. vzájemné ověřování umožňuje klientovi i Application Gateway ověřování navzájem. 

> [!NOTE]
> Doporučujeme používat TLS 1,2 se vzájemným ověřováním, protože TLS 1,2 bude v budoucnu pověřit. 

## <a name="mutual-authentication"></a>Vzájemné ověřování

Application Gateway podporuje vzájemné ověřování založené na certifikátech, kde můžete do Application Gateway nahrát certifikáty důvěryhodné klientské certifikační autority a brána tento certifikát použije k ověření klienta odesílajícího požadavek do brány. Díky nárůstu případů použití IoT a zvýšeným požadavkům na zabezpečení napříč obory poskytuje vzájemné ověřování způsob, jak spravovat a řídit, kteří klienti můžou komunikovat s vaším Application Gateway. 

Ke konfiguraci vzájemného ověřování se vyžaduje, aby se certifikát důvěryhodné certifikační autority klienta nahrál jako součást části ověřování klienta v profilu SSL. Aby bylo možné dokončit konfiguraci vzájemného ověřování, bude nutné profil SSL přidružit k naslouchacímu procesu. V klientském certifikátu, který nahráváte, musí být vždy certifikát kořenové certifikační autority. Můžete nahrát i řetěz certifikátů, ale řetěz musí zahrnovat certifikát od kořenové certifikační autority kromě tolika certifikátů Zprostředkující certifikační autority. 

Pokud například klientský certifikát obsahuje certifikát kořenové certifikační autority, několik zprostředkujících certifikátů certifikační autority a listový certifikát, zajistěte, aby se certifikát kořenové certifikační autority a všechny certifikáty zprostředkující certifikační autority nahrály do Application Gateway v jednom souboru. Další informace o tom, jak extrahovat certifikát důvěryhodné certifikační autority klienta, najdete v tématu [postup extrakce certifikátů certifikační autority důvěryhodných klientů](./mutual-authentication-certificate-management.md).

Pokud nahráváte řetěz certifikátů s kořenovou certifikační autoritou a certifikáty zprostředkující certifikační autority, musí se řetěz certifikátů nahrát jako soubor PEM nebo CER do brány. 

> [!NOTE] 
> Vzájemné ověřování je dostupné jenom u SKU Standard_v2 a WAF_v2. 

### <a name="certificates-supported-for-mutual-authentication"></a>Certifikáty podporované pro vzájemné ověřování

Application Gateway podporuje následující typy certifikátů:

- Certifikát CA (certifikační autorita): certifikát certifikační autority je digitální certifikát vydaný certifikační autoritou (CA).
- Certifikáty certifikační autority podepsané svým držitelem: klientské prohlížeče nedůvěřují těmto certifikátům a upozorní uživatele, že certifikát virtuální služby není součástí řetězce důvěryhodnosti. Certifikáty certifikační autority podepsané svým držitelem jsou vhodné pro testování nebo prostředí, kde správci kontrolují klienty a můžou bezpečně obejít výstrahy zabezpečení v prohlížeči. Provozní procesy by nikdy neměly používat certifikáty certifikační autority podepsané svým držitelem.

Další informace o tom, jak nastavit vzájemné ověřování, najdete v tématu [Konfigurace vzájemného ověřování pomocí Application Gateway](./mutual-authentication-portal.md).

> [!IMPORTANT]
> Při použití vzájemného ověřování se ujistěte, že jste do Application Gateway nahráli celý řetěz certifikátů CA důvěryhodných klientů. 

## <a name="additional-client-authentication-validation"></a>Další ověřování klientů

### <a name="verify-client-certificate-dn"></a>Ověřit rozlišující název klientského certifikátu

Máte možnost ověřit bezprostředního vystavitele klientského certifikátu a povolíte Application Gateway důvěřovat tomuto vystaviteli. Tato možnost je ve výchozím nastavení vypnutá, ale můžete ji povolit prostřednictvím portálu, PowerShellu nebo rozhraní příkazového řádku Azure CLI. 

Pokud se rozhodnete povolit Application Gateway k ověření bezprostředního vystavitele klientského certifikátu, tady je postup určení toho, co bude z odesílaných certifikátů extrahován rozlišující název vystavitele klientského certifikátu. 
* **Scénář 1:** Řetěz certifikátů obsahuje: kořenový certifikát – zprostředkující certifikát – certifikát na list 
    * Název subjektu *zprostředkujícího certifikátu* je to, co se Application Gateway extrahuje jako rozlišující název vystavitele certifikátu klienta a ověří se. 
* **Scénář 2:** Řetěz certifikátů zahrnuje: kořenový certifikát-intermediate1 certifikát – certifikát intermediate2 certifikátu – list certifikátu.
    * Název předmětu *Intermediate2 certifikátu* bude EXTRAHOVÁN jako rozlišující název vystavitele certifikátu klienta a bude ověřen proti. 
* **Scénář 3:** Řetěz certifikátů zahrnuje: kořenový certifikát-listový certifikát 
    * Název předmětu *kořenového certifikátu* bude extrahován a bude použit jako rozlišující název vystavitele certifikátu klienta.
* **Scénář 4:** Několik řetězů certifikátů se stejnou délkou ve stejném souboru. Řetěz 1 zahrnuje: certifikát Root Certificate-intermediate1 Certificate-list. Řetěz 2 zahrnuje: certifikát Root Certificate-intermediate2 Certificate-list. 
    * Název subjektu *certifikátu Intermediate1* bude EXTRAHOVÁN jako rozlišující název vystavitele certifikátu klienta.  
* **Scénář 5:** Několik řetězů certifikátů s různou délkou ve stejném souboru. Řetěz 1 zahrnuje: certifikát Root Certificate-intermediate1 Certificate-list. Řetěz 2 zahrnuje certifikát Root Certificate-intermediate2 certifikát-intermediate3 Certificate-list. 
    * Název subjektu *certifikátu Intermediate3* bude EXTRAHOVÁN jako rozlišující název vystavitele certifikátu klienta. 

> [!IMPORTANT]
> Doporučujeme nahrávat pouze jeden řetěz certifikátů na soubor. To je obzvláště důležité, pokud povolíte ověření rozlišujícího názvu klientského certifikátu. Nahráním více řetězů certifikátů v jednom souboru skončíte ve scénáři 4 nebo 5 a může dojít k problémům později v případě, že se certifikát klienta neshoduje s rozlišujícím názvem vystavitele certifikátu klienta Application Gateway extrahováním z řetězů. 

Další informace o extrakci řetězů certifikátů certifikační autority důvěryhodných klientů najdete v tématu [postup extrakce řetězů certifikátů certifikační autority důvěryhodných klientů](./mutual-authentication-certificate-management.md).

## <a name="server-variables"></a>Serverové proměnné 

Při vzájemném ověřování jsou k dispozici další proměnné serveru, které můžete použít k předání informací o klientském certifikátu na servery back-end za Application Gateway. Další informace o tom, které proměnné serveru jsou k dispozici a jak je používat, najdete v tématu [proměnné serveru](./rewrite-http-headers-url.md#mutual-authentication-server-variables-preview).

## <a name="next-steps"></a>Další kroky

Po získání informací o vzájemném ověřování pokračujte [v PowerShellu na nakonfigurovat Application Gateway se vzájemným ověřováním](./mutual-authentication-powershell.md) . tím vytvoříte Application Gateway pomocí vzájemného ověřování. 

