---
layout: post
title: "Eclipse: cannot open web.xml"
tags: [eclipse,web.xml]
date: 2007-06-18 01:01:01 +1000
---

> How to solve issue where Eclipse: cannot open web.xml. [Mirror of this Blogger post](https://robertmarkbramprogrammer.blogspot.com/2007/06/eclipse-cannot-open-webxml.html).

<p>I have an IBM Portlet project in RAD. Sometimes I cannot open web.xml. I get the error below when I try. The fix? Restarting RAD has fixed it twice so far... </P>

<pre>Unable to create this part due to an internal error. Reason for the failure: An unexpected exception was thrown.

java.lang.NullPointerException
 at org.eclipse.emf.ecore.resource.impl.ResourceSetImpl.getResource(Unknown Source)
 at org.eclipse.jem.internal.util.emf.workbench.ProjectResourceSetImpl.getResource(Unknown Source)
 at org.eclipse.wst.common.internal.emfworkbench.WorkbenchResourceHelper.getOrCreateResource(Unknown Source)
 at org.eclipse.wst.common.internal.emfworkbench.integration.EditModel.getResource(Unknown Source)
 at org.eclipse.wst.common.componentcore.internal.ArtifactEditModel.getResource(Unknown Source)
 at org.eclipse.wst.common.internal.emfworkbench.integration.EditModel.getPrimaryResource(Unknown Source)
 at org.eclipse.wst.common.internal.emfworkbench.integration.EditModel.getPrimaryRootObject(Unknown Source)
 at org.eclipse.wst.common.componentcore.ArtifactEdit.getContentModelRoot(Unknown Source)
 at com.ibm.etools.web.ui.presentation.WebEditor.initializeRootModelObject(Unknown Source)
 at com.ibm.etools.emf.workbench.ui.presentation.ArtifactEditMultiPageEditorPart.initialize(Unknown Source)
 at com.ibm.etools.emf.workbench.ui.presentation.ArtifactEditMultiPageEditorPart.createPages(Unknown Source)
 at org.eclipse.ui.part.MultiPageEditorPart.createPartControl(Unknown Source)
 at org.eclipse.ui.internal.EditorReference.createPartHelper(Unknown Source)
 at org.eclipse.ui.internal.EditorReference.createPart(Unknown Source)
 at org.eclipse.ui.internal.WorkbenchPartReference.getPart(Unknown Source)
 at org.eclipse.ui.internal.EditorReference.getEditor(Unknown Source)
 at org.eclipse.ui.internal.WorkbenchPage.busyOpenEditorBatched(Unknown Source)
 at org.eclipse.ui.internal.WorkbenchPage.busyOpenEditor(Unknown Source)
 at org.eclipse.ui.internal.WorkbenchPage.access$10(Unknown Source)
 at org.eclipse.ui.internal.WorkbenchPage$9.run(Unknown Source)
 at org.eclipse.swt.custom.BusyIndicator.showWhile(Unknown Source)
 at org.eclipse.ui.internal.WorkbenchPage.openEditor(Unknown Source)
 at org.eclipse.ui.internal.WorkbenchPage.openEditor(Unknown Source)
 at org.eclipse.ui.ide.IDE.openEditor(Unknown Source)
 at org.eclipse.ui.ide.IDE.openEditor(Unknown Source)
 at org.eclipse.ui.actions.OpenFileAction.openFile(Unknown Source)
 at org.eclipse.ui.actions.OpenSystemEditorAction.run(Unknown Source)
 at org.eclipse.ui.views.navigator.OpenActionGroup.runDefaultAction(Unknown Source)
 at org.eclipse.ui.views.navigator.MainActionGroup.runDefaultAction(Unknown Source)
 at org.eclipse.ui.views.navigator.ResourceNavigator.handleOpen(Unknown Source)
 at org.eclipse.ui.views.navigator.ResourceNavigator$6.open(Unknown Source)
 at org.eclipse.jface.viewers.StructuredViewer$2.run(Unknown Source)
 at org.eclipse.core.runtime.SafeRunner.run(Unknown Source)
 at org.eclipse.core.runtime.Platform.run(Unknown Source)
 at org.eclipse.ui.internal.JFaceUtil$1.run(Unknown Source)
 at org.eclipse.jface.util.SafeRunnable.run(Unknown Source)
 at org.eclipse.jface.viewers.StructuredViewer.fireOpen(Unknown Source)
 at org.eclipse.jface.viewers.StructuredViewer.handleOpen(Unknown Source)
 at org.eclipse.jface.viewers.StructuredViewer$6.handleOpen(Unknown Source)
 at org.eclipse.jface.util.OpenStrategy.fireOpenEvent(Unknown Source)
 at org.eclipse.jface.util.OpenStrategy.access$2(Unknown Source)
 at org.eclipse.jface.util.OpenStrategy$1.handleEvent(Unknown Source)
 at org.eclipse.swt.widgets.EventTable.sendEvent(Unknown Source)
 at org.eclipse.swt.widgets.Widget.sendEvent(Unknown Source)
 at org.eclipse.swt.widgets.Display.runDeferredEvents(Unknown Source)
 at org.eclipse.swt.widgets.Display.readAndDispatch(Unknown Source)
 at org.eclipse.ui.internal.Workbench.runEventLoop(Unknown Source)
 at org.eclipse.ui.internal.Workbench.runUI(Unknown Source)
 at org.eclipse.ui.internal.Workbench.createAndRunWorkbench(Unknown Source)
 at org.eclipse.ui.PlatformUI.createAndRunWorkbench(Unknown Source)
 at org.eclipse.ui.internal.ide.IDEApplication.run(Unknown Source)
 at org.eclipse.core.internal.runtime.PlatformActivator$1.run(Unknown Source)
 at org.eclipse.core.runtime.internal.adaptor.EclipseAppLauncher.runApplication(Unknown Source)
 at org.eclipse.core.runtime.internal.adaptor.EclipseAppLauncher.start(Unknown Source)
 at org.eclipse.core.runtime.adaptor.EclipseStarter.run(Unknown Source)
 at org.eclipse.core.runtime.adaptor.EclipseStarter.run(Unknown Source)
 at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
 at sun.reflect.NativeMethodAccessorImpl.invoke(Unknown Source)
 at sun.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source)
 at java.lang.reflect.Method.invoke(Unknown Source)
 at org.eclipse.core.launcher.Main.invokeFramework(Unknown Source)
 at org.eclipse.core.launcher.Main.basicRun(Unknown Source)
 at org.eclipse.core.launcher.Main.run(Unknown Source)
 at org.eclipse.core.launcher.Main.main(Unknown Source)
</pre>