---
title: Přidat certifikát do úložiště Java certifikační Autority
description: Zjistěte, jak přidat certifikát certifikační autority (CA) do úložiště certifikátů (cacerts) Java CA pro Twilio služby nebo Azure Service Bus.
services: ''
documentationcenter: java
author: rmcmurray
manager: mbaldwin
ms.assetid: d3699b0a-835c-43fb-844d-9c25344e5cda
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.openlocfilehash: 1c432aa9da9637675262313d935edd38bd7b698b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="adding-a-certificate-to-the-java-ca-certificates-store"></a>Přidání certifikátu do úložiště certifikátů certifikační Autority Java
Následující kroky ukazují, jak přidat certifikát certifikační autority (CA) do úložiště certifikátů (cacerts) Java certifikační Autority. Příklad používá se pro certifikát certifikační Autority vyžadované službou Twilio. Informace uvedené dále v tomto tématu popisuje postup instalace certifikátu certifikační Autority pro Azure Service Bus. 

Přidání certifikátu certifikační Autority před pomocí formátu ZIP vaší JDK a její přidání do projektu Azure můžete použít keytool **approot** složky, nebo může spustit úkol aplikace Azure spuštění, který používá keytool se přidat certifikát. (Tento příklad předpokládá, že přidáváte certifikát Certifikační autority před pomocí formátu ZIP vaší JDK.) Také v příkladu se používá konkrétní certifikát certifikační Autority, ale postup získání jiný certifikát certifikační Autority a jeho import do úložiště cacerts by být podobné.

## <a name="to-add-a-certificate-to-the-cacerts-store"></a>Přidat certifikát do úložiště cacerts
1. Na příkazový řádek správce, který je nastaven na vaše JDK **jdk\jre\lib\security** složky, spusťte následující příkaz k instalaci jaké certifikátů:
   
    `keytool -list -keystore cacerts`
   
    Zobrazí se výzva k zadání hesla, úložiště. Výchozí heslo je **changeit**. (Pokud chcete změnit heslo, naleznete v dokumentaci příkazu keytool na <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.) Tento příklad předpokládá, že certifikát s MD5 otisků 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 není uvedena v seznamu a, kterou chcete importovat (Tento konkrétní certifikát je vyžadován službou Twilio rozhraní API).
2. Tento certifikát získat ze seznamu certifikátů, které jsou uvedeny v [GeoTrust kořenové certifikáty](http://www.geotrust.com/resources/root-certificates/). Klikněte pravým tlačítkem myši na odkaz pro certifikát s 35:DE:F4:CF sériové číslo a uložte ho do **jdk\jre\lib\security** složky. Pro účely tohoto příkladu se uloží do souboru s názvem **Equifax\_zabezpečeného\_certifikát\_Authority.cer**.
3. Importujte certifikát pomocí následujícího příkazu:
   
    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`
   
    Pokud se zobrazí výzva k důvěřovat certifikátu s MD5 otiskem 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 reakce zadáním **y**.
4. Spusťte následující příkaz, který zajistěte, aby byl že certifikát certifikační Autority musí být úspěšně naimportována:
   
    `keytool -list -keystore cacerts`
5. Sadu JDK ZIP a přidejte ji do projektu Azure **approot** složky.

Informace o příkazu keytool najdete v tématu <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

## <a name="azure-root-certificates"></a>Azure kořenové certifikáty
Aplikace, které používají služby Azure (například Azure Service Bus) musí důvěřovat certifikátu Baltimore CyberTrust Root. (Od 15. dubna 2013, Azure začal migrace z GTE CyberTrust Root globální na Baltimore CyberTrust Root. Tato migrace trvalo několik měsíců).

Baltimore certifikát již je nainstalován v úložišti cacerts, takže nezapomeňte spustit **keytool-seznamu** příkaz nejprve se, zda již existuje.

Pokud potřebujete přidat Baltimore CyberTrust Root, má 02:00:00:b9 sériové číslo a SHA1 otisk prstu d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2 c: 78:db:28:52:ca:e4:74. Lze ji stáhnout z <https://cacert.omniroot.com/bc2025.crt>, uloží se do místního souboru s příponou **.cer**a poté importovat pomocí **keytool** jak je znázorněno v předchozích krocích.

## <a name="next-steps"></a>Další postup
Další informace o kořenové certifikáty používají v Azure najdete v tématu [Azure kořenový certifikát migrace](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx).

Další informace o Java najdete v tématu [Azure pro vývojáře v jazyce Java](/java/azure).

