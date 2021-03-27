---
title: Nouzové střídání certifikátů AD FS | Microsoft Docs
description: Tento článek vysvětluje, jak okamžitě odvolat a aktualizovat certifikáty AD FS.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 9035c0a91bbbd7493437c692540fcbb3136a094e
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612998"
---
# <a name="emergency-rotation-of-the-ad-fs-certificates"></a>Nouzové otočení certifikátů AD FS
V případě, že potřebujete okamžitě otočit AD FS certifikátů, můžete postupovat podle kroků uvedených níže v této části.

> [!IMPORTANT]
> Provedením následujících kroků v AD FS prostředí se okamžitě odvolají staré certifikáty.  Vzhledem k tomu, že se to dělá hned, normální čas obvykle povolený vašim federačním partnerům využívání nového certifikátu je předaný. Může dojít k výpadku služby jako u důvěryhodnosti aktualizace pro použití nových certifikátů.  To by mělo být vyřešeno, jakmile všichni federační partneři mají nové certifikáty.

> [!NOTE]
> Microsoft důrazně doporučuje používat k ochraně a zabezpečení certifikátů modul hardwarového zabezpečení (HSM).
> Další informace najdete v [modulu hardwarového zabezpečení](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#hardware-security-module-hsm) v části osvědčené postupy pro zabezpečení AD FS.

## <a name="determine-your-token-signing-certificate-thumbprint"></a>Určení kryptografického otisku podpisového certifikátu tokenu
Chcete-li odvolat starý podpisový certifikát tokenu, který AD FS aktuálně používá, je nutné určit kryptografický otisk certifikátu token-sigining.  K tomu použijte následující postup:

 1. Připojit ke službě Microsoft Online Service `PS C:\>Connect-MsolService`
 2. Zdokumentujte svůj místní a data podpisového certifikátu cloudového tokenu a data vypršení platnosti.
`PS C:\>Get-MsolFederationProperty -DomainName <domain>` 
 3.  Zkopírujte kryptografický otisk.  Později se použije k odebrání stávajících certifikátů.

Kryptografický otisk můžete taky získat pomocí správy AD FS, přechodem ke službě nebo certifikátům, kliknutím pravým tlačítkem na certifikát, vyberte Zobrazit certifikát a pak vyberte podrobnosti. 

## <a name="determine-whether-ad-fs-renews-the-certificates-automatically"></a>Určete, jestli AD FS automaticky obnoví certifikáty.
Ve výchozím nastavení je AD FS nakonfigurovaná tak, aby automaticky generovala certifikáty podepisování tokenů a dešifrování tokenů, a to v počátečním čase konfigurace i v případě, že se certifikáty blíží datu vypršení platnosti.

Můžete spustit následující příkaz Windows PowerShellu: `PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*` .

Vlastnost AutoCertificateRollover popisuje, jestli je AD FS nakonfigurovaná tak, aby se automaticky obnovil podpis tokenu a dešifrují se tokeny.  Pokud je AutoCertificateRollover nastavené na hodnotu TRUE, postupujte podle pokynů uvedených níže v tématu [generování nového certifikátu podepsaného svým držitelem, pokud je AutoCertificateRollover nastaven na TRUE].  Pokud je hodnota AutoCertificateRollover nastavená na FALSE, postupujte podle pokynů uvedených níže v tématu [generování nových certifikátů ručně, pokud je AutoCertificateRollover nastavené na FALSE].


## <a name="generating-new-self-signed-certificate-if-autocertificaterollover-is-set-to-true"></a>Generování nového certifikátu podepsaného svým držitelem, pokud je AutoCertificateRollover nastavené na TRUE
V této části vytvoříte **dva** podpisové certifikáty tokenů.  První bude používat příznak **-URGENT** , který nahradí aktuální primární certifikát hned.  Druhá se bude používat pro sekundární certifikát.  

>[!IMPORTANT]
>Důvodem, proč vytváříme dva certifikáty, je, že Azure obsahuje informace o předchozím certifikátu.  Když vytvoříte druhý, vynutíme si Azure, aby uvolnil informace o původním certifikátu a nahradil ho informacemi o druhém certifikátu.
>
>Pokud nevytvoříte druhý certifikát a aktualizujete ho v Azure, může být možné, že starý certifikát pro podepisování tokenů ověřuje uživatele.

Pomocí následujících kroků můžete vygenerovat nové podpisové certifikáty tokenů.

 1. Ujistěte se, že jste přihlášeni k primárnímu AD FSmu serveru.
 2. Spusťte Windows PowerShell jako správce. 
 3. Zkontrolujte, jestli je vaše AutoCertificateRollover nastavené na true.
`PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`
 4. Vygenerujte nový podpisový certifikát tokenu: `Update-ADFSCertificate –CertificateType token-signing -Urgent` .
 5. Ověřte aktualizaci spuštěním následujícího příkazu: `Get-ADFSCertificate –CertificateType token-signing`
 6. Teď vygenerujte druhý podpisový certifikát tokenu: `Update-ADFSCertificate –CertificateType token-signing` .
 7. Aktualizaci můžete ověřit tak, že znovu spustíte následující příkaz: `Get-ADFSCertificate –CertificateType token-signing`


## <a name="generating-new-certificates-manually-if-autocertificaterollover-is-set-to-false"></a>Ruční generování nových certifikátů, pokud je AutoCertificateRollover nastavené na FALSE
Pokud nepoužíváte výchozí automaticky generovaný podpisový certifikát tokenu a dešifrovací certifikáty tokenů, musíte tyto certifikáty obnovit a nakonfigurovat ručně.  To zahrnuje vytvoření dvou nových podpisových certifikátů tokenů a jejich importování.  Pak povýšíte jeden na primární, odvoláte starý certifikát a nakonfigurujete druhý certifikát jako sekundární certifikát.

Nejprve je třeba získat od certifikační autority dva nové certifikáty a importovat je do osobního úložiště certifikátů místního počítače na každém federačním serveru. Pokyny najdete v článku [Import certifikátu](https://technet.microsoft.com/library/cc754489.aspx) .

>[!IMPORTANT]
>Důvodem, proč vytváříme dva certifikáty, je, že Azure obsahuje informace o předchozím certifikátu.  Když vytvoříte druhý, vynutíme si Azure, aby uvolnil informace o původním certifikátu a nahradil ho informacemi o druhém certifikátu.
>
>Pokud nevytvoříte druhý certifikát a aktualizujete ho v Azure, může být možné, že starý certifikát pro podepisování tokenů ověřuje uživatele.

### <a name="to-configure-a-new-certificate-as-a-secondary-certificate"></a>Postup konfigurace nového certifikátu jako sekundárního certifikátu
Pak musíte nakonfigurovat jeden certifikát jako sekundární podpisový certifikát tokenu AD FS nebo ho dešifrovat a pak ho povýšit na primární.

1. Po importu certifikátu. Otevřete konzolu **pro správu AD FS** .
2. Rozbalte položku **Služba** a pak vyberte možnost **certifikáty**.
3. V podokně Akce klikněte na **přidat Token-Signing certifikát**.
4. Vyberte nový certifikát ze seznamu zobrazených certifikátů a pak klikněte na OK.

### <a name="to-promote-the-new-certificate-from-secondary-to-primary"></a>Zvýšení úrovně nového certifikátu ze sekundárního na primární
Teď, když je nový certifikát naimportovaný a nakonfigurovaný v AD FS, musíte nastavit jako primární certifikát.
1. Otevřete konzolu **pro správu AD FS** .
2. Rozbalte položku **Služba** a pak vyberte možnost **certifikáty**.
3. Klikněte na podpisový certifikát sekundárního tokenu.
4. V podokně **Akce** klikněte na možnost **nastavit jako primární**. Na výzvu k potvrzení klikněte na Ano.
5. Po zvýšení úrovně nového certifikátu jako primárního certifikátu byste měli odebrat starý certifikát, protože ho můžete dál používat. Další informace najdete v části [Odebrání starých certifikátů](#remove-your-old-certificates) níže.  

### <a name="to-configure-the-second-certificate-as-a-secondary-certificate"></a>Jak nakonfigurovat druhý certifikát jako sekundární certifikát
Teď, když jste přidali první certifikát a nastavili ho jako primární a odebrali jste ho starý, naimportujte druhý certifikát.  Pak je nutné nakonfigurovat certifikát jako sekundární podpisový certifikát tokenu AD FS.

1. Po importu certifikátu. Otevřete konzolu **pro správu AD FS** .
2. Rozbalte položku **Služba** a pak vyberte možnost **certifikáty**.
3. V podokně Akce klikněte na **přidat Token-Signing certifikát**.
4. Vyberte nový certifikát ze seznamu zobrazených certifikátů a pak klikněte na OK.

## <a name="update-azure-ad-with-the-new-token-signing-certificate"></a>Aktualizace služby Azure AD pomocí nového podpisového certifikátu tokenů
Otevřete Modul Microsoft Azure Active Directory pro Windows PowerShell. Případně otevřete Windows PowerShell a spusťte příkaz. `Import-Module msonline`

Připojte se ke službě Azure AD spuštěním následujícího příkazu: `Connect-MsolService` a pak zadejte svoje přihlašovací údaje globálního správce.

>[!Note]
> Pokud tyto příkazy spouštíte na počítači, který není primárním federačním serverem, zadejte nejdřív následující příkaz: `Set-MsolADFSContext –Computer <servername>` . Nahraďte <servername> názvem AD FS serveru. Po zobrazení výzvy zadejte přihlašovací údaje správce serveru AD FS.

Volitelně můžete zkontrolovat, jestli je aktualizace nutná, pomocí kontroly aktuálních informací o certifikátu ve službě Azure AD. Uděláte to tak, že spustíte následující příkaz: `Get-MsolFederationProperty` . Po zobrazení výzvy zadejte název federované domény.

Pokud chcete aktualizovat informace o certifikátu v Azure AD, spusťte následující příkaz: `Update-MsolFederatedDomain` a po zobrazení výzvy zadejte název domény.

>[!Note]
> Pokud se při spuštění tohoto příkazu zobrazí chyba, spusťte následující příkaz: `Update-MsolFederatedDomain –SupportMultipleDomain` a po zobrazení výzvy zadejte název domény.

## <a name="replace-ssl-certificates"></a>Výměna certifikátů SSL
V případě, že je potřeba nahradit podpisový certifikát tokenem z důvodu ohrožení zabezpečení, měli byste také odvolat a nahradit certifikáty SSL pro AD FS a servery WAP.  

Odvolání certifikátů SSL se musí provádět u certifikační autority (CA), která certifikát vystavila.  Tyto certifikáty jsou často vydávané poskytovateli třetích stran, jako je GoDaddy.  Příklad najdete v tématu (odvolání certifikátu | Certifikáty SSL – GoDaddy nám pomůžou.  Další informace najdete v tématu [jak odvolávání certifikátů funguje](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619754(v=ws.10)?redirectedfrom=MSDN).

Jakmile se starý certifikát SSL odvolá a vytvoří se nový, můžete nahradit certifikáty SSL. Další informace najdete v tématu [Výměna certifikátu SSL pro AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs).


## <a name="remove-your-old-certificates"></a>Odebrání starých certifikátů
Po nahrazení starých certifikátů byste měli původní certifikát odebrat, protože ho můžete dál používat. . Chcete-li to provést, postupujte podle následujících kroků:.  Chcete-li to provést, postupujte podle následujících kroků:

1. Ujistěte se, že jste přihlášeni k primárnímu AD FSmu serveru.
2. Spusťte Windows PowerShell jako správce. 
4. Odebrání starého podpisového certifikátu tokenu: `Remove-ADFSCertificate –CertificateType token-signing -thumbprint <thumbprint>` .

## <a name="updating-federation-partners-who-can-consume-federation-metadata"></a>Aktualizace federačních partnerů, kteří můžou využívat federační metadata
Pokud jste obnovili a nakonfigurovali nový certifikát pro podpis nebo dešifrování tokenu tokenu, musíte zajistit, aby všichni vaši federační partneři (partneři prostředků nebo organizace poskytující účty, kteří jsou zastoupeni ve vašem AD FS pomocí vztahů důvěryhodnosti předávající strany a vztahu důvěryhodnosti zprostředkovatele deklarací) vybrali nové certifikáty.

## <a name="updating-federation-partners-who-can-not-consume-federation-metadata"></a>Aktualizace federačních partnerů, kteří nemůžou využívat federační metadata
Pokud vaše federační partneři nemůžou využívat vaše federační metadata, musíte jim ručně poslat veřejný klíč nového šifrovacího certifikátu pro podepisování tokenů nebo tokenu. Odešlete nový veřejný klíč certifikátu (soubor. cer nebo. P7B, pokud chcete zahrnout celý řetěz) ke všem vašim organizacím prostředků nebo partnerům organizace účtů (reprezentované ve vašem AD FSi vztahy důvěryhodnosti předávající strany a vztahy důvěryhodnosti zprostředkovatele deklarací identity). Mají partneři na své straně implementovat změny pro důvěřování novým certifikátům.



## <a name="revoke-refresh-tokens-via-powershell"></a>Odvolání obnovovacích tokenů přes PowerShell
Teď chceme odvolat aktualizační tokeny pro uživatele, kteří je můžou mít, a vynutit, aby se znovu přihlašovat a získali nové tokeny.  Tím se uživatelé přihlašují z telefonu, z aktuálních relací webové pošty spolu s dalšími položkami, které používají tokeny a aktualizují tokeny.  Informace najdete [tady](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0&preserve-view=true) a můžete také odkázat na to, jak [odvolat přístup uživatelů v Azure Active Directory](../../active-directory/enterprise-users/users-revoke-access.md).

## <a name="next-steps"></a>Další kroky

- [Správa certifikátů SSL v AD FS a WAP ve Windows serveru 2016](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)
- [Získejte a nakonfigurujte certifikáty podepisování a dešifrování tokenů pro AD FS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn781426(v=ws.11)#updating-federation-partners)
- [Prodloužit platnost federačních certifikátů pro Microsoft 365 a Azure Active Directory](how-to-connect-fed-o365-certs.md)



















