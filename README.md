# Resume-Parsing
Resume parsing is the process of converting unstructured resume data into structured format. It provides output in XML or JSON format.

<%@ Language="VBScript" %>
<%'<!--#include file="config.asp" -->
'////// Configration Setting //////



'///// VARIABLES START 
 mStSubmit=Request("submit")
 if mStSubmit<>"" then
   url=Trim(Request.Form("txtUrl"))
   key=Trim(Request.Form("txtKey"))
   version=Trim (Request.Form ("txtVersion"))
    SubUserkey=Trim (Request.Form ("txtSubUserkey"))


'//////// SOAP XML TO BE SENT
SoapStr = "<soapenv:Envelope xmlns:soapenv='http://schemas.xmlsoap.org/soap/envelope/' xmlns:rch='http://RchilliResumeParser'>"&_
   "<soapenv:Header/>"&_
   "<soapenv:Body>"&_
      "<rch:parseResume>"&_
         "<rch:url>"& url &"</rch:url>"&_
         "<rch:userkey>"& key &"</rch:userkey>"&_
         "<rch:version>"& version &"</rch:version>"&_
         "<rch:subUserId>"& SubUserkey &"</rch:subUserId>"&_
      "</rch:parseResume>"&_
   "</soapenv:Body>"&_
   "</soapenv:Envelope>"
   
   

'////// SOAP XML END

'///////// XML PROCESSED 

SET objXMLDOC = SERVER.CREATEOBJECT("Msxml2.ServerXMLHTTP")
objXMLDOC.setTimeouts 50000, 60000, 100000, 100000
objXMLDOC.OPEN "POST", LINK, False
objXMLDOC.setRequestHeader "Content-Type", "text/xml"
objXMLDOC.send(SoapStr)
'//// Save the xml file in a specified path

XMLString = Replace(Replace(objXMLDOC.responseText,"&lt;","<"),"&gt;",">")
SET xmlDoc = CREATEOBJECT("Microsoft.XMLDOM")
xmlDoc.async = "false"

'Response.Write("<br>"&FilePath)
''xmlDoc.load(FilePath)

If objXMLDOC.status = 200 Then
  ' no server error
  If objXMLDOC.responseXML.parseError.errorCode = 0 Then
    ' no parsing error either
        'objXMLDOC.responseXML.save FilePath
		xmlDoc.loadXml(XMLString)		
    Else
        Response.Write("No File Saved! Parse Error")  
  End If
End If



'//////// PROCESS END 

mStSubmit = Request("submit")
'if mStSubmit <>""   then
'ParseResumeResult	= xmlDoc.getElementsByTagName("ParseResumeResult").ITEM(0).text	

If xmlDoc.parseError.errorCode <> 0 Then 'parser error found 
    'EMAIL PARSER ERROR INFORMATION
    BodyText 	= "Error in the XML Code: " & xmlDoc.parseError.errorCode & VbCrLf &_
    "Error Reason: " & xmlDoc.parseError.reason & VbCrLf &_
    "Error Line: " & xmlDoc.parseError.line & ""
    Response.Write(BodyText)
    Response.End()
Else '/// No Error in XML Loading 

    'Response.Write("No Error in XML Loading")
	'Response.Write("<br/><br/><br/> write xml <br/><br/><br/>")
	'Response.Write(xmlDoc.xml)
	'Response.Write("<br/><br/><br/><br/><br/><br/>")
    Dim oNode,oNodeLengh
    Set oNode = xmlDoc.getElementsByTagName("ResumeParserData") 
    oNodeLengh = oNode.length 
    '///// if root tag exists then proceed and get all elements
	
	'Response.Write("Node length : ")
	'Response.Write(oNodelengh)
	
    If oNodeLengh > 0 then
        ResumeParserData = oNode.item(0).text
        'Response.Write("<br>ResumeParserData: "&ResumeParserData)
          
        Set oNode = xmlDoc.getElementsByTagName("FirstName") 
        oNodeLengh = oNode.length 
        If oNodeLengh > 0 then
            FirstName = oNode.item(0).text
            Response.Write("<br><b>FirstName: "& FirstName)
        End If	
       
        Set oNode = xmlDoc.getElementsByTagName("Middlename") 
        oNodeLengh = oNode.length 
        If oNodeLengh > 0 then
            Middlename = oNode.item(0).text
            Response.Write("<br><b>Middlename: "& Middlename)
        End If	

        Set oNode = xmlDoc.getElementsByTagName("LastName") 
        oNodeLengh = oNode.length 
        If oNodeLengh > 0 then
            LastName = oNode.item(0).text
            Response.Write("<br><b>LastName: "& LastName)
        End If
        Set oNode = xmlDoc.getElementsByTagName("Email") 
        oNodeLengh = oNode.length 
        If oNodeLengh > 0 then
            Email = oNode.item(0).text
            Response.Write("<br><b>Email: "& Email)
        End If 
        Set oNode = xmlDoc.getElementsByTagName("Phone") 
        oNodeLengh = oNode.length 
        If oNodeLengh > 0 then
            Phone = oNode.item(0).text
            Response.Write("<br><b>Phone: "& Phone)
        End If  
    End If	
End if 
    Set xmlDoc = Nothing
    set oNode = Nothing
      end if
'End if

'similarly you can read all other nodes too.
%>

<!-- Design Page -->
<html>
<head>
    <title>Response Parse XMLData</title>
</head>
<body>
    <table>
        <tr>
            <td>
                <form name="hell" action="" method="post">
                    <table width="426" height="457">
                        <tr>
                            <td style="width: 153" height="21">
                                <strong>Resume URL</strong><br>
                            </td>
                            <td style="width: 263" height="21">
                                <input type="text" name="txtUrl" value="" size="53" /></td>
                        </tr>
                        <tr>
                            <td width="153" height="21">
                                <strong>User KEY</strong></td>
                            <td width="263" height="21">
                                <input type="text" name="txtKey" value="" size="20" /></td>
                        </tr>
                        <tr>
                            <td width="153" height="31">
                                <strong>Version</strong></td>
                            <td width="263" height="31">
                                <input type="text" name="txtVersion" value="" size="20" /></td>
                        </tr>
                        <tr>
                            <td width="153" height="31">
                                <strong>Sub User Key</strong></td>
                            <td width="263" height="31">
                                <input type="text" name="txtSubUserkey" value="" size="20" /></td>
                        </tr>
                        <tr>
                            <td width="153" height="40">
                                <input name="Submit" type="submit" value="ResumeParser" /></td>
                            <td width="263" height="40">&nbsp;
                                
                            </td>
                        </tr>
                        <tr>
                            <td colspan="2" width="420" height="324">
                                <textarea name="txtName1" rows="18" cols="50"><%=XMLString%></textarea></td>
                        </tr>
                    </table>
                </form>
            </td>
        </tr>
    </table>
</body>
</html>
