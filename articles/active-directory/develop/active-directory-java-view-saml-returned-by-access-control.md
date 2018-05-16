---
title: Zobrazení SAML vrácený službě Řízení přístupu (Java)
description: Zjistěte, jak chcete-li zobrazit SAML vrácený službě Řízení přístupu v aplikace Java hostované v Azure.
services: active-directory
documentationcenter: java
author: rmcmurray
manager: mbaldwin
ms.assetid: 6cd216f9-eb43-46b4-b30d-f194d0ae2d48
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.custom: aaddev
ms.openlocfilehash: f6cdb2877541bd584f881b9dfeff38500d19ab82
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-view-saml-returned-by-the-azure-access-control-service"></a>Postup zobrazení SAML vrácený službě Řízení přístupu Azure
Tento průvodce vám ukáže, jak chcete zobrazit základní zabezpečení kontrolního výrazu SAML (Markup Language) vrátíte zpět do aplikace služby Řízení přístupu (ACS) Azure. V příručce vychází [postup ověření webového uživatele s Azure přístup k řízení služby pomocí Eclipse](active-directory-java-authenticate-users-access-control-eclipse.md) tématu, tím, že poskytuje kód, který zobrazí informace o tomto SAML. Hotová aplikace bude vypadat podobně jako následující.

![Příklad výstupu SAML][saml_output]

Další informace o služby ACS, najdete v článku [další kroky](#next_steps) části.

> [!NOTE]
> Ovládací prvek filtru Azure přístup služeb je náhled technologie komunity. Jako předběžné verze softwaru není oficiálně společností Microsoft.
> 
> 

## <a name="prerequisites"></a>Požadavky
K dokončení úkolů v tomto průvodci, dokončete ukázku najdete na adrese [postup ověření webového uživatele s Azure přístup k řízení služby pomocí Eclipse](active-directory-java-authenticate-users-access-control-eclipse.md) a použít jej jako výchozí bod pro tento kurz.

## <a name="add-the-jspwriter-library-to-your-build-path-and-deployment-assembly"></a>Přidání knihovny JspWriter na vaše sestavení a nasazení cesta sestavení
Přidání knihovny, který obsahuje **javax.servlet.jsp.JspWriter** třída pro vaše nasazení a cesta k sestavení sestavení. Pokud používáte Tomcat, je knihovna **jsp api.jar**, který se nachází v Apache **lib** složky.

1. V prostředí Eclipse v prohlížeči projektu klikněte pravým tlačítkem na **MyACSHelloWorld**, klikněte na tlačítko **cesta sestavení**, klikněte na tlačítko **konfigurovat cestu sestavení**, klikněte na tlačítko **knihovny** a pak klikněte **přidat externí JARs**.
2. V **JAR výběr** dialogové okno, přejděte na nezbytné JAR, vyberte ho a pak klikněte na tlačítko **otevřete**.
3. S **vlastnosti pro MyACSHelloWorld** stále otevřená, klikněte na dialogové okno **nasazení sestavení**.
4. V **webové nasazení sestavení** dialogové okno, klikněte na tlačítko **přidat**.
5. V **nové – Direktiva Assembly** dialogové okno, klikněte na tlačítko **položky cesta sestavení Java** a pak klikněte na **Další**.
6. Vyberte příslušnou knihovnu a klikněte na **Dokončit**.
7. Klikněte na tlačítko **OK** zavřete **vlastnosti pro MyACSHelloWorld** dialogové okno.

## <a name="modify-the-jsp-file-to-display-saml"></a>Upravte soubor JSP zobrazíte SAML
Upravit **index.jsp** použít následující kód.

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {

            try
            {
                String nodeName;
                int nChild, i;

                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");

                   // Attributes.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }

                   // Child nodes.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                    {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    

                                 // If it is a text node, just print the text.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Print out the child node names.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                        for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);

                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Separate the names.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Finish the sentence.
                                            out.print(".");
                                        }

                                     }
                                     out.println("<br>");

                                     // Process the child nodes.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>

        <%
        try
        {
            String data  = (String) request.getAttribute("ACSSAML");

            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();

            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA);
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();

            // Iterate the child nodes of the doc.
            NodeList list = doc.getChildNodes();

            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e)
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }

        %>
    </body>
    </html>

## <a name="run-the-application"></a>Spuštění aplikace
1. Spusťte aplikaci v emulátoru počítače nebo nasadit do Azure, pomocí kroků popsaných v [postup ověření webového uživatele s Azure přístup k řízení služby pomocí Eclipse](active-directory-java-authenticate-users-access-control-eclipse.md).
2. Spusťte prohlížeč a otevřete webovou aplikaci. Po přihlášení do aplikace se zobrazí informace o SAML, včetně assertion zabezpečení poskytované poskytovatelem identity.

## <a name="next-steps"></a>Další postup
K dalšímu prozkoumat funkce služby ACS a experimentovat s sofistikovanější scénáři, najdete v tématu [2.0 služby Řízení přístupu][Access Control Service 2.0].

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[How to Authenticate Web Users with Azure Access Control Service Using Eclipse]: active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
