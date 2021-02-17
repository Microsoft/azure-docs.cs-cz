---
title: Certifikáty verze Preview Azure Firewall Premium
description: Pro správnou konfiguraci kontroly TLS na Azure Firewall Premium Preview musíte nakonfigurovat a nainstalovat certifikáty zprostředkující certifikační autority.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3914a82903c293cf1a8306b5ecc1f542fef83e72
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549645"
---
# <a name="azure-firewall-premium-preview-certificates"></a>Certifikáty verze Preview Azure Firewall Premium 

> [!IMPORTANT]
> Azure Firewall Premium je momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Aby bylo možné správně nakonfigurovat Azure Firewall Premium Preview TLS, musíte zadat platný certifikát zprostředkující certifikační autority a uložit ho do trezoru klíčů Azure.

## <a name="certificates-used-by-azure-firewall-premium-preview"></a>Certifikáty, které používá Azure Firewall Premium Preview

Existují tři typy certifikátů, které se používají při typickém nasazení:

- **Certifikát zprostředkující certifikační autority (certifikát certifikační autority)**

   Certifikační autorita (CA) je organizace, která je důvěryhodná k podepisování digitálních certifikátů. CA ověřuje identitu a legitimitu společnosti nebo jednotlivce, který požaduje certifikát. Pokud je ověření úspěšné, certifikační autorita vydá podepsaný certifikát. Když server prezentuje certifikát klientovi (například ve webovém prohlížeči) během ověřování protokolem SSL/TLS, pokusí se ověřit podpis na seznamu *známých správných* přihlášení. Webové prohlížeče jsou obvykle dodávány se seznamy certifikačních autorit, které implicitně důvěřují za účelem identifikace hostitelů. Pokud tato autorita není v seznamu, stejně jako u některých webů, které podepisují vlastní certifikáty, prohlížeč upozorní uživatele, že certifikát není podepsán rozpoznanou autoritou, a požádá uživatele, pokud chtějí pokračovat v komunikaci s neověřenou lokalitou.

- **Certifikát serveru (certifikát webu)**

   Certifikát přidružený k určitému názvu domény Pokud má web platný certifikát, znamená to, že certifikační autorita provedla kroky k ověření, že webová adresa skutečně patří do této organizace. Když zadáte adresu URL nebo přejdete na zabezpečený web, prohlížeč zkontroluje certifikát o následující vlastnosti:
   - Adresa webu se shoduje s adresou na certifikátu.
   - Certifikát je podepsaný certifikační autoritou, kterou prohlížeč rozpoznává jako *důvěryhodnou* autoritu.
   
   Občas se uživatelé mohou připojit k serveru s nedůvěryhodným certifikátem. Azure Firewall odstraní připojení, jako by server ukončil připojení.

- **Certifikát kořenové certifikační autority (kořenový certifikát)**

   Certifikační autorita může vystavovat více certifikátů ve formě stromové struktury. Kořenový certifikát je certifikát nejvyšší úrovně stromu.

Azure Firewall Premium Preview dokáže zachytit odchozí přenos HTTP/S a automaticky vygenerovat certifikát serveru pro `www.website.com` . Tento certifikát se vygeneruje pomocí certifikátu zprostředkující certifikační autority, který poskytnete. Aby tento postup fungoval, musí být v prohlížeči a klientských aplikacích pro koncové uživatele důvěryhodný certifikát kořenové certifikační autority vaší organizace nebo certifikát zprostředkující certifikační autority. 

:::image type="content" source="media/premium-certificates/certificate-process.png" alt-text="Proces certifikátu":::

## <a name="intermediate-ca-certificate-requirements"></a>Požadavky na certifikát zprostředkující certifikační autority

Ujistěte se, že váš certifikát certifikační autority splňuje tyto požadavky:

- Při nasazení jako tajný klíč Key Vault musíte použít bez hesla (PFX) (PKCS12) s certifikátem a privátním klíčem.

- Musí se jednat o jeden certifikát a neměl by obsahovat celý řetězec certifikátů.  

- Musí být platný po dobu jednoho roku dopředný.  

- Musí to být privátní klíč RSA s minimální velikostí 4096 bajtů.  

- Musí mít `KeyUsage` příponu označenou jako kritickou s `KeyCertSign` příznakem (RFC 5280; 4.2.1.3 Key Usage).

- Musí mít `BasicContraints` rozšíření označené jako kritické (RFC 5280; 4.2.1.9 Basic Constraints).  

- `CA`Příznak musí být nastaven na hodnotu true.

- Délka cesty musí být větší než nebo rovna hodnotě jedna.

## <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](../key-vault/general/overview.md) je úložiště tajné databáze spravované platformou, které můžete použít k ochraně tajných klíčů, klíčů a certifikátů TLS/SSL. Azure Firewall Premium podporuje integraci s Key Vault pro certifikáty serveru, které jsou připojené k zásadám brány firewall.
 
Konfigurace trezoru klíčů:

- Do trezoru klíčů musíte importovat stávající certifikát s jeho dvojicí klíčů. 
- Případně můžete také použít tajný klíč trezoru klíčů, který je uložený jako soubor PFX s kódováním bez hesla (Base-64).  Soubor PFX je digitální certifikát, který obsahuje privátní klíč i veřejný klíč.
- Doporučuje se použít Import certifikátu certifikační autority, protože umožňuje nakonfigurovat výstrahu na základě data vypršení platnosti certifikátu.
- Po importu certifikátu nebo tajného klíče v trezoru klíčů musíte definovat zásady přístupu, aby bylo možné udělení identity získat přístup k certifikátu nebo tajnému kódu.
- Poskytnutý certifikát certifikační autority musí být důvěryhodný pro vaši úlohu Azure. Zajistěte, aby byly správně nasazeny.

Můžete buď vytvořit nebo znovu použít existující spravovanou identitu přiřazenou uživatelem, kterou Azure Firewall používá k načtení certifikátů od Key Vault vaším jménem. Další informace najdete v tématu [co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md) 

## <a name="configure-a-certificate-in-your-policy"></a>Konfigurace certifikátu v zásadách

Pokud chcete nakonfigurovat certifikát certifikační autority v zásadě brány firewall Premium, vyberte zásadu a pak vyberte **Kontrola TLS (Preview)**. Na stránce Kontrola protokolu **TLS** vyberte **povoleno** . Pak vyberte certifikát certifikační autority v Azure Key Vault, jak je znázorněno na následujícím obrázku:

:::image type="content" source="media/premium-certificates/tls-inspection.png" alt-text="Diagram přehledu Azure Firewall Premium":::
 
> [!IMPORTANT]
> Pokud chcete zobrazit a nakonfigurovat certifikát z Azure Portal, musíte do zásady přístupu Key Vault přidat svůj uživatelský účet Azure. Poskytněte uživatelskému účtu oprávnění k **získání** a **zobrazení seznamu** pod **oprávněními tajného** kódu.
   :::image type="content" source="media/premium-certificates/secret-permissions.png" alt-text="Zásada přístupu Azure Key Vault":::


## <a name="troubleshooting"></a>Řešení potíží

Pokud je certifikát certifikační autority platný, ale v rámci kontroly TLS nemáte přístup k plně kvalifikovanému názvu domény nebo adresám URL, podívejte se na následující položky:

- Ujistěte se, že je certifikát webového serveru platný.  

- Zajistěte, aby byl certifikát kořenové certifikační autority nainstalovaný v klientském operačním systému.  

- Ujistěte se, že prohlížeč nebo klient HTTPS obsahuje platný kořenový certifikát. Aplikace Firefox a některé další prohlížeče mohou mít zvláštní zásady certifikace.  

- Ujistěte se, že typ cíle adresy URL v pravidle vaší aplikace pokrývá správnou cestu a všechny ostatní hypertextové odkazy vložené do cílové stránky HTML. Zástupné znaky můžete použít k snadnému pokrytí celé požadované cesty URL.  


## <a name="next-steps"></a>Další kroky

- [Další informace o funkcích Azure Firewall Premium](premium-features.md)
