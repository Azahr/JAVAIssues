###Issue: 
If we have a GUI which is offering Insert,Delete,Update, Once the user submits the page there might be the chance
of resubmitting the request in following ways.

`01. By Clicking on Submit/Save/Delete/Update button for multiple times`
`02. After submitting the request clicking refresh button in the browser`
`03. After submitting the request clicking back button and resubmitting the request.`

###Solution

Hi
Our application is a State Based one...we have to allow user to submit form only once. We are using struts1.1,servlet2.3,jstl,jboss.

StrutsConfig.xml
-----------------

<form-beans>
<form-bean name="testForm" type="com.bluephant.form.TestForm" />
</form-beans>

<action-mappings>

<action path="/test" type="com.bluephant.form.TestPreAction" scope="session">
<forward name="success" path="/test.jsp" />
</action>

<action path="/test2" 
type="com.bluephant.form.TestPostAction" 
name="testForm" 
validate="true" 
input="/test.jsp" 
scope="request">
<forward name="next" path="/test_next.jsp" />
</action>

TestPreAction
-------------
public ActionForward execute(ActionMapping mapping,ActionForm form,
HttpServletRequest request,
HttpServletResponse response)
throws Exception { 

saveToken(request);
return (mapping.findForward("success"));

}
TestPostAction
----------------
public ActionForward execute(ActionMapping mapping,ActionForm form,
HttpServletRequest request,
HttpServletResponse response)
throws Exception { 

if (isTokenValid(request)) {
resetToken(request);
saveToken(request);
//valid token process the form
return (mapping.findForward("next"));
}else{
MessageResources messages = getResources(request);
ActionErrors errors = new ActionErrors();
errors.add("org.apache.struts.action.GLOBAL_ERROR", new ActionError( "dvd.error.invalidToken" ) );
saveErrors( request, errors );
//duplicate submission
return (new ActionForward(mapping.getInput()));
} 
test.jsp
---------
<html:form action="/test2.do" method="POST">

If i use browser back button once i submit form isTokenValid(request)) in TestPostAction always returns the true..... instead of false.....

it working fine if i use refresh button.
