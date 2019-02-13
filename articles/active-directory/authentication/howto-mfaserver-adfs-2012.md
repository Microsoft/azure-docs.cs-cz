---
title: Server Azure MFA s AD FS ve Windows Serveru
description: Tento článek popisuje, jak začít se službami Azure Multi-Factor Authentication a AD FS v systému Windows Server 2012 R2 a 2016.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30f35010b5072c89779b8030055a2d37beb58b0d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200434"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-in-windows-server"></a>Konfigurace serveru Azure Multi-Factor Authentication pro práci se službou AD FS ve Windows Serveru

Pokud používáte službu AD FS (Active Directory Federation Services) a chcete zabezpečit cloudové nebo místní prostředky, můžete nakonfigurovat Azure Multi-Factor Authentication Server pro práci se službou AD FS. Tato konfigurace aktivuje u citlivých koncových bodů dvoustupňové ověřování.

V tomto článku se podíváme na to, jak používat Azure Multi-Factor Authentication Server s AD FS v systému Windows Server 2012 R2 nebo Windows Server 2016. Další informace najdete v tématu [Zabezpečení cloudových a lokálních prostředků pomocí Microsoft Azure Multi-Factor Authentication Serveru s AD FS 2.0](howto-mfaserver-adfs-2.md).

## <a name="secure-windows-server-ad-fs-with-azure-multi-factor-authentication-server"></a>Zabezpečení Windows Serveru AD FS pomocí Azure Multi-Factor Authentication Serveru

Azure Multi-Factor Authentication Server můžete instalovat různě:

* Nainstalujte Azure Multi-Factor Authentication Server lokálně na stejném serveru jako AD FS.
* Nainstalujte adaptér Azure Multi-Factor Authentication místně na server AD FS a Multi-Factor Authentication Server potom nainstalujte na jiný počítač.

Před zahájením se ujistěte, že znáte následující informace:

* Na váš server AD FS není potřeba instalovat Azure Multi-Factor Authentication Server. Na Windows Server 2012 R2 nebo Windows Server 2016, kde běží služba AD FS, je pak ale nutné nainstalovat adaptér Multi-Factor Authentication pro AD FS. Server můžete nainstalovat na jiný počítač, pokud adaptér AD FS nainstalujete samostatně na federační server AD FS. Samostatně adaptér instalujte podle následujících postupů.
* Pokud v organizaci používáte metody ověření pomocí textové zprávy nebo mobilní aplikace, řetězce definované v Nastavení společnosti obsahují zástupný text „<$*application_name*$>“. V MFA Serveru verze 7.1 můžete zadat název aplikace, který nahrazuje tento zástupný text. Ve verzi 7.0 nebo dřívější se tento zástupný text při použití adaptéru AD FS nenahradí automaticky. U těchto starších verzí při zabezpečování AD FS odeberte tento zástupný text z příslušných řetězců.
* Účet, se kterým se přihlašujete, musí mít uživatelská práva k vytváření skupin zabezpečení ve službě Active Directory.
* Průvodce instalací adaptéru Multi-Factor Authentication AD FS vytvoří v instanci Active Directory skupinu zabezpečení s názvem PhoneFactor Admins. Potom přidá účet služby AD FS vaší služby federace do této skupiny. Ověřte, že se v řadiči domény vytvořila skupina PhoneFactor Admins a mezi jejími členy je účet služby AD FS. Pokud je to nutné, přidejte v řadiči domény účet služby AD FS do skupiny PhoneFactor Admins ručně.
* Informace o tom, jak instalovat sadu SDK webové služby pomocí uživatelského portálu, najdete v tématu popisujícím [nasazení uživatelského portálu pro Azure Multi-Factor Authentication Server](howto-mfaserver-deploy-userportal.md).

### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>Instalace Azure Multi-Factor Authentication Server místně na serveru AD FS

1. Stáhněte a nainstalujte Azure Multi-Factor Authentication Server na server AD FS. Informace o instalaci se dočtete v tématu [Začínáme s Azure Multi-Factor Authentication Serverem](howto-mfaserver-deploy.md).
2. Na Azure Multi-Factor Authentication Serveru klikněte na ikonu **AD FS**. Vyberte možnosti **Povolit zápis uživatele** a **Povolit uživatelům výběr metody**.
3. Vyberte další možnosti, které byste chtěli pro svou organizaci nastavit.
4. Klikněte na **Instalovat adaptér služby AD FS**.
   
   <center>![Cloud](./media/howto-mfaserver-adfs-2012/server.png)</center>

5. Pokud se zobrazí okno služby Active Directory, znamená to dvě věci. Počítač je připojený k doméně a konfigurace Active Directory pro zabezpečení komunikace mezi adaptérem AD FS a ověřovací službou Multi-Factor Authentication není kompletní. Kliknutím na možnost **Další** konfiguraci automaticky dokončíte. Nebo můžete zaškrtnout políčko **Vynechat automatickou konfiguraci služby Active Directory a konfigurovat nastavení ručně**. Klikněte na **Další**.
6. Pokud se zobrazí okno místní skupiny, znamená to dvě věci. Počítač není připojený k doméně a konfigurace místní skupiny pro zabezpečení komunikace mezi adaptérem AD FS a ověřovací službou Multi-Factor Authentication není kompletní. Kliknutím na možnost **Další** konfiguraci automaticky dokončíte. Nebo můžete zaškrtnout políčko **Vynechat automatickou konfiguraci místní skupiny a konfigurovat nastavení ručně**. Klikněte na **Další**.
7. V průvodci instalací klikněte na tlačítko **Další**. Azure Multi-Factor Authentication Server vytvoří skupinu PhoneFactor Admins a přidá do ní účet služby AD FS.
   <center>![Cloud](./media/howto-mfaserver-adfs-2012/adapter.png)</center>
8. V kroku **Spustit instalační program** klikněte na **Další**.
9. V instalačním programu adaptéru Multi-Factor Authentication AD FS klikněte na **Další**.
10. Po dokončení instalace klikněte na **Zavřít**.
11. Nainstalovaný adaptér je třeba zaregistrovat ve službě AD FS. Otevřete Windows PowerShell a spusťte následující příkaz:<br>
    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
    <center>![Cloud](./media/howto-mfaserver-adfs-2012/pshell.png)</center>
12. Upravte globální zásady ověřování ve službě AD FS tak, abyste mohli nově zaregistrovaný adaptér použít. V konzole pro správu služby AD FS přejděte k uzlu **Zásady ověřování**. V oddílu **Multi-Factor Authentication** klikněte na odkaz **Upravit** vedle oddílu **Globální nastavení**. V okně **Upravit globální zásady ověření** vyberte jako další metodu ověření **Multi-Factor Authentication** a klikněte na **OK**. Adaptér je zaregistrovaný jako WindowsAzureMultiFactorAuthentication. Restartujte službu AD FS, aby se registrace projevila.

<center>![Cloud](./media/howto-mfaserver-adfs-2012/global.png)</center>

Multi-Factor Authentication Server máte teď nastavený jako dodatečného poskytovatele ověření vedle služby AD FS.

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>Samostatná instalace adaptéru AD FS pomocí sady SDK webové služby

1. Nainstalujte sadu SDK webové služby na server, kde běží Multi-Factor Authentication Server.
2. Z adresáře \Program Files\Multi-Factor Authentication Server zkopírujte na server, kam plánujete nainstalovat adaptér služby AD FS, tyto soubory:
   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config
3. Spusťte instalační soubor MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. V instalačním programu adaptéru Multi-Factor Authentication AD FS spusťte instalaci kliknutím na tlačítko **Další**.
5. Po dokončení instalace klikněte na **Zavřít**.

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>Úprava souboru MultiFactorAuthenticationAdfsAdapter.config
Postupujte podle těchto kroků a upravte soubor MultiFactorAuthenticationAdfsAdapter.config:

1. Uzel **UseWebServiceSdk** nastavte na **true**.  
2. Hodnotu **WebServiceSdkUrl** nastavte na URL sady SDK webové služby pro Multi-Factor Authentication. Příklad:  *https://contoso.com/&lt; název_certifikátu&gt;/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx*, kde *název_certifikátu* je název vašeho certifikátu.  
3. Upravte skript Register-Register-MultiFactorAuthenticationAdfsAdapter.ps1 tak, že na konec příkazu `Register-AdfsAuthenticationProvider` přidáte `-ConfigurationFilePath &lt;path&gt;`, kde *&lt;path&gt;* je úplná cesta k souboru MultiFactorAuthenticationAdfsAdapter.config.

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>Konfigurace sady SDK webové služby pomocí uživatelského jména a hesla

Por konfiguraci sady SDK webové služby existují dvě možnosti. První možností je použití uživatelského jména a hesla, druhou je použití klientského certifikátu. Pro první možnost postupujte podle těchto kroků, nebo je přeskočte, pokud chcete použít druhou možnost.  

1. Hodnotu **WebServiceSdkUsername** nastavte na účet, který je členem skupiny zabezpečení PhoneFactor Admins. Zadávejte ve formátu &lt;doména&gt;&#92;&lt;uživatelské jméno&gt;.  
2. Hodnotu **WebServiceSdkPassword** nastavte na odpovídající heslo k tomuto účtu.

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>Konfigurace sady SDK webové služby pomocí klientského certifikátu

Pokud nechcete použít uživatelské jméno a heslo, postupujte podle těchto kroků a nakonfigurujte sadu SDK webové služby pomocí klientského certifikátu.

1. Od certifikační autority získejte klientský certifikát pro server, na kterém běží sada SDK webové služby. Přečtěte si, jak [získat klientský certifikát](https://technet.microsoft.com/library/cc770328.aspx).  
2. Importujte klientský certifikát do osobního úložiště certifikátů na místním serveru, kde běží sada SDK webové služby. Ujistěte se, že se veřejný certifikát této autority nachází v úložišti certifikátů Důvěryhodné kořenové certifikáty.  
3. Exportujte veřejný a privátní klíč klientského certifikátu do souboru .pfx.  
4. Exportujte veřejný klíč ve formátu Base64 do souboru .cer.  
5. Ve Správci serveru zkontrolujte, že je nainstalovaná funkce Web Server (IIS)\Web Server\Security\IIS Client Certificate Mapping Authentication. Pokud nainstalovaná není, přidejte ji kliknutím na **Přidat role a funkce**.  
6. Ve Správci IIS na webové stránce, která obsahuje virtuální adresář sady SDK webové služby, poklikejte na **Editor konfigurace**. Je důležité vybrat na web, a ne virtuální adresář.  
7. Přejděte do oddílu **system.webServer/security/authentication/iisClientCertificateMappingAuthentication**.  
8. Hodnotu enabled nastavte na **true**.  
9. Hodnotu oneToOneCertificateMappingsEnabled nastavte na **true**.  
10. Klikněte na tlačítko **...** vedle položky oneToOneMappings a potom klikněte na odkaz **Přidat**.  
11. Otevřete soubor .cer s klíčem ve formátu Base64, který jste exportovali. Odeberte řetězce *-----BEGIN CERTIFICATE-----* a *-----END CERTIFICATE-----* a veškerá zalomení řádků. Výsledný řetězec zkopírujte.  
12. Hodnotu certificate nastavte na řetězec zkopírovaný v předchozím kroku.  
13. Hodnotu enabled nastavte na **true**.  
14. Nastavte userName na účet, který je členem skupiny zabezpečení PhoneFactor Admins. Zadávejte ve formátu &lt;doména&gt;&#92;&lt;uživatelské jméno&gt;.  
15. Heslo nastavte na odpovídající heslo k tomuto účtu a pak Editor konfigurace zavřete.  
16. Klikněte na odkaz **Použít**.  
17. Ve virtuálním adresáři sady SDK webové služby poklikejte na možnost **Ověřování**.  
18. Zkontrolujte, že jsou možnosti Zosobnění technologie ASP.NET a Základní ověřování **zapnuté** a všechny ostatní položky **vypnuté**.  
19. Ve virtuálním adresáři sady SDK webové služby poklikejte na **Nastavení SSL**.  
20. Nastavte Klientské certifikáty na **Přijmout** a klikněte na **Použít**.  
21. Soubor .pfx, který jste exportovali, zkopírujte na server, kde běží adaptér AD FS.  
22. Importujte soubor .pfx do osobního úložiště certifikátů na místním počítači.  
23. Klikněte pravým tlačítkem, vyberte možnost **Spravovat soukromé klíče** a účtu, který jste použili pro přihlášení ke službě AD FS, udělte oprávnění ke čtení.  
24. Otevřete klientský certifikát a zkopírujte kryptografický otisk z karty **Podrobnosti**.  
25. V souboru MultiFactorAuthenticationAdfsAdapter.config nastavte **WebServiceSdkCertificateThumbprint** na řetězec zkopírovaný v předchozím kroku.  

V prostředí PowerShell spusťte skript \Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1. Adaptér je zaregistrovaný jako WindowsAzureMultiFactorAuthentication. Restartujte službu AD FS, aby se registrace projevila.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Zabezpečení prostředků Azure AD pomocí služby AD FS

K zabezpečení cloudových prostředků nastavte pravidlo deklarace identity tak, aby služba Active Directory Federation Services vyslala deklaraci identity multipleauthn, když uživatel úspěšně provede dvoustupňové ověření. Tato deklarace identity se předá Azure AD. Postupujte takto:

1. Otevřete správu služby AD FS.
2. Na levé straně vyberte **Vztahy důvěryhodnosti předávající strany**.
3. Klikněte pravým tlačítkem na **Platforma identit Microsoft Office 365** a vyberte **Upravit pravidla deklarací identity**.

   ![Cloud](./media/howto-mfaserver-adfs-2012/trustedip1.png)

4. V pravidlech transformace vystavení klikněte na **Přidat pravidlo**.

   ![Cloud](./media/howto-mfaserver-adfs-2012/trustedip2.png)

5. V Průvodci přidáním pravidla – deklarace identity transformace vyberte v rozevíracím seznamu **Předávat nebo filtrovat příchozí deklarace** a klikněte na **Další**.

   ![Cloud](./media/howto-mfaserver-adfs-2012/trustedip3.png)

6. Pojmenujte pravidlo.
7. Jako typ příchozí deklarace identity vyberte **Odkazy na metody ověřování**.
8. Vyberte **Předávat všechny hodnoty deklarací identity**.
    ![Průvodce přidáním pravidla deklarace identity transformace](./media/howto-mfaserver-adfs-2012/configurewizard.png)
9. Klikněte na **Dokončit**. Uzavřete konzolu pro správu služby AD FS.

## <a name="troubleshooting-logs"></a>Řešení potíží s protokoly

S řešením potíží s adaptérem AD FS pro MFA Server vám pomůžou následující kroky, kterými se povolí dodatečné protokolování.

1. V rozhraní MFA Serveru otevřete část AD FS a zaškrtněte políčko **Povolit protokolování**.
2. Na každém serveru AD FS pomocí nástroje **regedit.exe** vytvořte klíč registru `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Positive Networks\PhoneFactor\InstallPath` s řetězcovou hodnotou `C:\Program Files\Multi-Factor Authentication Server\` (nebo jiný adresář podle vašeho výběru).  **Nezapomeňte, že zpětné lomítko na konci je důležité.**
3. Vytvořte adresář `C:\Program Files\Multi-Factor Authentication Server\Logs` (nebo jiný adresář, který jste použili v **kroku 2**).
4. Udělte účtu služby AD FS přístup pro úpravy k adresáři protokolů.
5. Restartujte službu AD FS.
6. Ověřte, že se v adresáři protokolů vytvořil soubor `MultiFactorAuthAdfsAdapter.log`.

## <a name="related-topics"></a>Související témata

Pomoc při řešení potíží najdete v tématu [Nejčastější dotazy ke službě Azure Multi-Factor Authentication](multi-factor-authentication-faq.md).
