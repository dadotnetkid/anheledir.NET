---
layout: post
section-type: post
title: Visual Studio 2010 Extension - Unicode Converter 1.0 (with Tutorial)
category: Unknown
tags: []
---
<p><a href="http://visualstudiogallery.msdn.microsoft.com/en-us/fcc87763-32e7-4870-8289-f18ce37b9764"><img class="wlDisabledImage" style="border-right-width: 0px; margin: 0px 10px 10px 0px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" src="http://anheledirwp.blob.core.windows.net/wordpress/2010/08/splashscreen.png" border="0" alt="Unicode Converter V1.0" width="200" height="133" align="left" /></a>For quite some time I&rsquo;m using a tool a co-worker has written to convert some text into &ldquo;websafe&rdquo; text. That&rsquo;s implying the convertion of special characters into html entities wherever possible, otherwise in their corresponding unicode notation <em>(&amp;#0000;</em>), replacing more then one space in a row with non-breaking spaces <em>(&amp;nbsp;</em>), replacing single line-breaks with the corresponding html-tag (<em>&lt;br /&gt;</em>) and also enclosing paragraphs with their html-tag <em>(&lt;p&gt;&hellip;&lt;/p&gt;</em>). Texts converted in this manner are having less or even no encoding problems in any webbrowsers and you don&rsquo;t have to fiddle around with manual adding those paragraph tags.</p>
<p>I&rsquo;m using this tool a lot when working with html files directly in Notepad++ or a similar texteditor. But since that time I&rsquo;m now mainly working within Visual Studio. You may argue that you can add the text in the design-view and Visual Studio is converting it for you. But not all special chars are converted and when the text is some kind of rich edit (<em>e.g. from Word</em>) you get a lot of unwanted formatting, too! So you could paste it into a plain-text editor, copy it back to the clipboardf and then paste it into Vis&hellip;. For God&rsquo;s sake! Let&rsquo;s abadon this thought immediately before someone will really do it this way! Saying it with a borrowed term: <strong>There&rsquo;s an app for that!</strong> <img class="wlEmoticon wlEmoticon-winkingsmile" style="border-bottom-style: none; border-right-style: none; border-top-style: none; border-left-style: none" src="http://anheledirwp.blob.core.windows.net/wordpress/2010/08/wlEmoticonwinkingsmile.png" alt="Winking smile" /></p>
<p><strong><img class="wlDisabledImage" style="border-right-width: 0px; margin: 0px 0px 0px 10px; display: inline; border-top-width: 0px; border-bottom-width: 0px; border-left-width: 0px" src="http://anheledirwp.blob.core.windows.net/wordpress/2010/08/web.png" border="0" alt="" width="48" height="48" align="right" />First you have to make a tough decission</strong>: Go straight to the <a href="http://visualstudiogallery.msdn.microsoft.com/en-us/fcc87763-32e7-4870-8289-f18ce37b9764">Visual Studio Gallery</a>, download, install and just use my &ldquo;<a href="http://visualstudiogallery.msdn.microsoft.com/en-us/fcc87763-32e7-4870-8289-f18ce37b9764">Unicode Converter</a>&rdquo;-Extension. Or you read on and I will explain a few things about how I solved some problems. Oh, and just in case: there is a link to the sourcecode of the extension at the end of this article! [more]</p>
<p>I started with a new &ldquo;<em>Shared Add-In</em>&rdquo;-project, entered all the standard stuff and took a look at the created files. Great, there are a lot of comments and even if you never had created a Visual Studio extension before (<em>like me!</em>) you quickly grasp the base-correlations. So, first step was to implement the main logic: The convertion of a given string. I was allowed to use the prepared hashtables with the html-entities and other special strings, after all a 1.5MB ressource-file. To avoid using the <strong>ResourceReader</strong>-class I converted that <strong>.rersource-file</strong> into a <strong>.resx-file</strong> with the <strong>Resource File Generator</strong> (<a href="http://msdn.microsoft.com/en-us/library/ccec7sz1(v=vs.80).aspx">Resgen.exe</a>). Filling my hastables with the data from the resource-file starts in the <strong>Initialize()</strong> method of the extension, right after adding my command to the Visual Studio menu:</p>
<pre style="background: #f6f8ff; color: #000020"><span style="color: #200080; font-weight: bold">protected</span> <span style="color: #200080; font-weight: bold">override</span> <span style="color: #200080; font-weight: bold">void</span> Initialize<span style="color: #308080">(</span><span style="color: #308080">)</span> <span style="color: #406080">{</span>
    <span style="color: #200080; font-weight: bold">base</span><span style="color: #308080">.</span>Initialize<span style="color: #308080">(</span><span style="color: #308080">)</span><span style="color: #406080">;</span>
    OleMenuCommandService mcs <span style="color: #308080">=</span>
        GetService<span style="color: #308080">(</span><span style="color: #200080; font-weight: bold">typeof</span><span style="color: #308080">(</span>IMenuCommandService<span style="color: #308080">)</span><span style="color: #308080">)</span> <span style="color: #200080; font-weight: bold">as</span> OleMenuCommandService<span style="color: #406080">;</span>
    <span style="color: #200080; font-weight: bold">if</span> <span style="color: #308080">(</span><span style="color: #200080; font-weight: bold">null</span> <span style="color: #308080">!</span><span style="color: #308080">=</span> mcs<span style="color: #308080">)</span> <span style="color: #406080">{</span>
        CommandID menuCommandID <span style="color: #308080">=</span>
            <span style="color: #200080; font-weight: bold">new</span> CommandID<span style="color: #308080">(</span>
                GuidList<span style="color: #308080">.</span>guidUnicodeConverterCmdSet<span style="color: #308080">,</span>
                <span style="color: #308080">(</span><span style="color: #200080; font-weight: bold">int</span><span style="color: #308080">)</span>PkgCmdIDList<span style="color: #308080">.</span>cmdidUnicode<span style="color: #308080">)</span><span style="color: #406080">;</span>
        MenuCommand menuItem <span style="color: #308080">=</span>
            <span style="color: #200080; font-weight: bold">new</span> MenuCommand<span style="color: #308080">(</span>MenuItemCallback<span style="color: #308080">,</span> menuCommandID<span style="color: #308080">)</span><span style="color: #406080">;</span>
        mcs<span style="color: #308080">.</span>AddCommand<span style="color: #308080">(</span>menuItem<span style="color: #308080">)</span><span style="color: #406080">;</span>
    <span style="color: #406080">}</span>
    fillHash<span style="color: #308080">(</span><span style="color: #308080">)</span><span style="color: #406080">;</span>
<span style="color: #406080">}</span></pre>
<p>The actual convertion is happening in three <em>foreach</em>-loops, nothing special here. So let&rsquo;s take a look at the <strong>MenuItemCallback()</strong> method that is called whenever my command is started. Let&rsquo;s enumerate what we want to do here:</p>
<ol>
<li><span style="color: #dcddde;">Get the currently selected text from the editor</span> </li>
<li><span style="color: #dcddde;">Convert this text</span> </li>
<li><span style="color: #dcddde;">Write the converted text back to the editor, replacing the original one</span> </li>
</ol>
<p>Sounds pretty easy, doesn&rsquo;t it? Well, <strong>step 1 and 2</strong> are not quite difficult, but <strong>step 3</strong> needs some considerations because our <strong>Span</strong> with the selected text is a <em>readonly</em>-property. So we lookup the index of our selected text, delete the old Span and insert a newly created span with our converted text at the very same position. Phew, that wasn&rsquo;t that hard, was it? Here we go:</p>
<pre style="background: #f6f8ff; color: #000020"><span style="color: #200080; font-weight: bold">private</span> <span style="color: #200080; font-weight: bold">void</span> MenuItemCallback<span style="color: #308080">(</span><span style="color: #200080; font-weight: bold">object</span> sender<span style="color: #308080">,</span> EventArgs e<span style="color: #308080">)</span> <span style="color: #406080">{</span>
    IVsUIShell uiShell <span style="color: #308080">=</span>
        <span style="color: #308080">(</span>IVsUIShell<span style="color: #308080">)</span>GetService<span style="color: #308080">(</span><span style="color: #200080; font-weight: bold">typeof</span><span style="color: #308080">(</span>SVsUIShell<span style="color: #308080">)</span><span style="color: #308080">)</span><span style="color: #406080">;</span>
    Guid clsid <span style="color: #308080">=</span> Guid<span style="color: #308080">.</span>Empty<span style="color: #406080">;</span>
    IWpfTextView view <span style="color: #308080">=</span> GetActiveTextView<span style="color: #308080">(</span><span style="color: #308080">)</span><span style="color: #406080">;</span>
    String selectedText <span style="color: #308080">=</span>
        view<span style="color: #308080">.</span>Selection<span style="color: #308080">.</span>SelectedSpans<span style="color: #308080">[</span><span style="color: #008c00">0</span><span style="color: #308080">]</span><span style="color: #308080">.</span>GetText<span style="color: #308080">(</span><span style="color: #308080">)</span><span style="color: #406080">;</span>
    var allText <span style="color: #308080">=</span> view<span style="color: #308080">.</span>TextSnapshot<span style="color: #308080">.</span>GetText<span style="color: #308080">(</span><span style="color: #308080">)</span><span style="color: #406080">;</span>
    <span style="color: #200080; font-weight: bold">int</span> pos <span style="color: #308080">=</span> allText<span style="color: #308080">.</span>IndexOf<span style="color: #308080">(</span>selectedText<span style="color: #308080">)</span><span style="color: #406080">;</span>
    OptionPageGrid page <span style="color: #308080">=</span>
        <span style="color: #308080">(</span>OptionPageGrid<span style="color: #308080">)</span>GetDialogPage<span style="color: #308080">(</span><span style="color: #200080; font-weight: bold">typeof</span><span style="color: #308080">(</span>OptionPageGrid<span style="color: #308080">)</span><span style="color: #308080">)</span><span style="color: #406080">;</span>

    convertUnicode<span style="color: #308080">(</span><span style="color: #200080; font-weight: bold">ref</span> selectedText<span style="color: #308080">)</span><span style="color: #406080">;</span>
    <span style="color: #200080; font-weight: bold">if</span> <span style="color: #308080">(</span>page<span style="color: #308080">.</span>replaceSpaces<span style="color: #308080">)</span>
        replaceDoubleSpace<span style="color: #308080">(</span><span style="color: #200080; font-weight: bold">ref</span> selectedText<span style="color: #308080">)</span><span style="color: #406080">;</span>
    <span style="color: #200080; font-weight: bold">if</span> <span style="color: #308080">(</span>page<span style="color: #308080">.</span>replaceNewline<span style="color: #308080">)</span>
        replaceNewLine<span style="color: #308080">(</span><span style="color: #200080; font-weight: bold">ref</span> selectedText<span style="color: #308080">)</span><span style="color: #406080">;</span>

    Span deleteSpan <span style="color: #308080">=</span> view<span style="color: #308080">.</span>Selection<span style="color: #308080">.</span>SelectedSpans<span style="color: #308080">[</span><span style="color: #008c00">0</span><span style="color: #308080">]</span><span style="color: #406080">;</span>
    view<span style="color: #308080">.</span>TextBuffer<span style="color: #308080">.</span>Delete<span style="color: #308080">(</span>deleteSpan<span style="color: #308080">)</span><span style="color: #406080">;</span>
    view<span style="color: #308080">.</span>TextBuffer<span style="color: #308080">.</span>Insert<span style="color: #308080">(</span>pos<span style="color: #308080">,</span> selectedText<span style="color: #308080">)</span><span style="color: #406080">;</span>
<span style="color: #406080">}</span></pre>
<p>Before I did a first test-run I wanted to add my new command to the context menu of the Visual Studio editor instead of creating a new menu entry. So, next stop: The <strong>Visual Studio Command Table</strong>, a XML-file with the extension .vsct. It took a few minutes until I understood what happend in there. But after some <strong>Bing&rsquo;ing</strong> (<em>gosh, that sounds awfull!</em> <img class="wlEmoticon wlEmoticon-sadsmile" style="border-bottom-style: none; border-right-style: none; border-top-style: none; border-left-style: none" src="http://anheledirwp.blob.core.windows.net/wordpress/2010/08/wlEmoticonsadsmile.png" alt="Sad smile" />) I found the lines I need to change to place my command into the context menu:</p>
<pre style="background: #f6f8ff; color: #000020"><span style="color: #0057a6">&lt;</span><span style="color: #333385">Group</span> <span style="color: #474796">guid</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">guidUnicodeConverterCmdSet</span><span style="color: #1060b6">"</span> <span style="color: #474796">id</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">UnicodeMenuGroup</span><span style="color: #1060b6">"</span> <span style="color: #474796">priority</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">0x0600</span><span style="color: #1060b6">"</span><span style="color: #0057a6">&gt;</span>
    <span style="color: #0057a6">&lt;</span><span style="color: #333385">Parent</span> <span style="color: #474796">guid</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">guidSHLMainMenu</span><span style="color: #1060b6">"</span> <span style="color: #474796">id</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">IDM_VS_CTXT_CODEWIN</span><span style="color: #1060b6">"</span><span style="color: #0057a6">/&gt;</span>
<span style="color: #0057a6">&lt;/</span><span style="color: #333385">Group</span><span style="color: #0057a6">&gt;</span></pre>
<p>Compiling, installing the Add-In, restarting Visual Studio and&hellip; <strong>where is my context menu entry?!</strong> I opened a simple html file and tried to convert some text in it, but my extension didn&rsquo;t show up. Well, actually it DID show up but I searched for it at the wrong place: When in a <strong>codefile (.cs)</strong> I could happily see my context menu entry and it worked like a charm. But why did it only appears in the code editor and <strong>not in the html code-view</strong>?</p>
<p>I was Bing&rsquo;ing again like &ldquo;<em>Big Ben</em>&rdquo; (<em>knee-slapper&hellip;</em>) but all I found were some cryptical information about .h-files with a C++ syntax. There were some GUIDs similiar to those I saw in my command table but when I tried using them as a parent I got some compile-errors. Some time-consuming investigations later (<em>I did not found a hint even in the msdn</em>) I found a small tool, the <a href="http://code.msdn.microsoft.com/VSCTPowerToy">VSCT PowerToy</a> in the MSDN Code Gallery. It&rsquo;s &ldquo;<em>a read-only viewer for Visual Studio 2010 that you can use to explore the commands associated with a VSPackage, and with Visual Studio itself. You can quickly search for any existing commands in the Visual Studio IDE. By browsing through the command groups, GUIDs and IDs, priorities, and other properties of existing commands, you can more easily place and integrate the commands of your own VSPackage.</em>&rdquo; &ndash; sounds pretty good to me: I have no clue how to do it, so I look at others how they accomplished it! <img class="wlEmoticon wlEmoticon-winkingsmile" style="border-bottom-style: none; border-right-style: none; border-top-style: none; border-left-style: none" src="http://anheledirwp.blob.core.windows.net/wordpress/2010/08/wlEmoticonwinkingsmile.png" alt="Winking smile" /></p>
<p>After playing around with the tool for a while I found what I was looking for, the <strong>GUIDs</strong> and <strong>IDs</strong> of the context menu used by the html-, aspx-, aspx.cs-, &hellip; editors. Yepp, all those editors have <strong>their own context menu</strong>. It&rsquo;s even splitted to <strong>html in aspx-files</strong>,<strong> inline-c#</strong> in aspx-files and <strong>inline-vb</strong> in aspx-files. That&rsquo;s pretty cool when you want to make an extension that&rsquo;s only working with a subset of all editors or languages! To test my newly found GUIDs and IDs (<strong>CMDSETID_HtmEdGrp</strong> &amp; <strong>IDMX_HTM_SOURCE_HTML</strong>) I added them as &ldquo;<em>parent</em>&rdquo; to my group and&hellip; my build failed again. This GUID and ID are unknown &ndash; corresponding to the compiler. Wait, there was a section within my .vsct-file to define my own command-names with their GUID. Perhaps I can add a few more..?</p>
<p>There you are! Even if those GUIDs and IDs are internally used by Visual Studio you have to define them for your extension first. The VSCT PowerToy can switch the view and instead of the name I get the corresponding GUIDs and IDs. After defining them as a <strong>GuidSymbol</strong> my project compiled without an error and it even showed my context menu entry in the html-editor. Strike!</p>
<pre style="background: #f6f8ff; color: #000020"><span style="color: #0057a6">&lt;</span><span style="color: #333385">GuidSymbol</span> <span style="color: #474796">name</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">CMDSETID_HtmEdGrp</span><span style="color: #1060b6">"</span>
            <span style="color: #474796">value</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">{d7e8c5e1-bdb8-11d0-9c88-0000f8040a53}</span><span style="color: #1060b6">"</span><span style="color: #0057a6">&gt;</span>
    <span style="color: #0057a6">&lt;</span><span style="color: #333385">IDSymbol</span> <span style="color: #474796">name</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">IDMX_HTM_SOURCE_BASIC</span><span style="color: #1060b6">"</span> <span style="color: #474796">value</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">0x32</span><span style="color: #1060b6">"</span> <span style="color: #0057a6">/&gt;</span>
    <span style="color: #0057a6">&lt;</span><span style="color: #333385">IDSymbol</span> <span style="color: #474796">name</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">IDMX_HTM_SOURCE_HTML</span><span style="color: #1060b6">"</span> <span style="color: #474796">value</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">0x33</span><span style="color: #1060b6">"</span> <span style="color: #0057a6">/&gt;</span>
    <span style="color: #0057a6">&lt;</span><span style="color: #333385">IDSymbol</span> <span style="color: #474796">name</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">IDMX_HTM_SOURCE_SCRIPT</span><span style="color: #1060b6">"</span> <span style="color: #474796">value</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">0x34</span><span style="color: #1060b6">"</span> <span style="color: #0057a6">/&gt;</span>
    <span style="color: #0057a6">&lt;</span><span style="color: #333385">IDSymbol</span> <span style="color: #474796">name</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">IDMX_HTM_SOURCE_ASPX</span><span style="color: #1060b6">"</span> <span style="color: #474796">value</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">0x35</span><span style="color: #1060b6">"</span> <span style="color: #0057a6">/&gt;</span>
    <span style="color: #0057a6">&lt;</span><span style="color: #333385">IDSymbol</span> <span style="color: #474796">name</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">IDMX_HTM_SOURCE_ASAX</span><span style="color: #1060b6">"</span> <span style="color: #474796">value</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">0x3B</span><span style="color: #1060b6">"</span> <span style="color: #0057a6">/&gt;</span>
    <span style="color: #0057a6">&lt;</span><span style="color: #333385">IDSymbol</span> <span style="color: #474796">name</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">IDMX_HTM_SOURCE_ASPX_CODE</span><span style="color: #1060b6">"</span> <span style="color: #474796">value</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">0x36</span><span style="color: #1060b6">"</span> <span style="color: #0057a6">/&gt;</span>
    <span style="color: #0057a6">&lt;</span><span style="color: #333385">IDSymbol</span> <span style="color: #474796">name</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">IDMX_HTM_SOURCE_ASAX_CODE</span><span style="color: #1060b6">"</span> <span style="color: #474796">value</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">0x3C</span><span style="color: #1060b6">"</span> <span style="color: #0057a6">/&gt;</span>
    <span style="color: #0057a6">&lt;</span><span style="color: #333385">IDSymbol</span> <span style="color: #474796">name</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">IDMX_HTM_SOURCE_ASPX_CODE_VB</span><span style="color: #1060b6">"</span> <span style="color: #474796">value</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">0x37</span><span style="color: #1060b6">"</span> <span style="color: #0057a6">/&gt;</span>
    <span style="color: #0057a6">&lt;</span><span style="color: #333385">IDSymbol</span> <span style="color: #474796">name</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">IDMX_HTM_SOURCE_ASAX_CODE_VB</span><span style="color: #1060b6">"</span> <span style="color: #474796">value</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">0x3D</span><span style="color: #1060b6">"</span> <span style="color: #0057a6">/&gt;</span>
    <span style="color: #0057a6">&lt;</span><span style="color: #333385">IDSymbol</span> <span style="color: #474796">name</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">IDMX_HTM_SOURCE_ASMX_CODE</span><span style="color: #1060b6">"</span> <span style="color: #474796">value</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">0x38</span><span style="color: #1060b6">"</span> <span style="color: #0057a6">/&gt;</span>
    <span style="color: #0057a6">&lt;</span><span style="color: #333385">IDSymbol</span> <span style="color: #474796">name</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">IDMX_HTM_SOURCE_ASMX_CODE_VB</span><span style="color: #1060b6">"</span> <span style="color: #474796">value</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">0x39</span><span style="color: #1060b6">"</span> <span style="color: #0057a6">/&gt;</span>
<span style="color: #0057a6">&lt;/</span><span style="color: #333385">GuidSymbol</span><span style="color: #0057a6">&gt;</span></pre>
<p>The next challenge was a no-brainer compared to the last one: I want my extension show up in the context menu not only of the code editor or the html-editor but in several of them at once. A solution was quickly found: With <strong>CommandPlacements</strong> you can add a command to several places so I added my extension to a few of them in case they make sense to me (&ldquo;<em>Where could you have some text that has to be websafe?</em>&rdquo;).</p>
<pre style="background: #f6f8ff; color: #000020"><span style="color: #0057a6">&lt;</span><span style="color: #333385">CommandPlacements</span><span style="color: #0057a6">&gt;</span>
    <span style="color: #0057a6">&lt;</span><span style="color: #333385">CommandPlacement</span> <span style="color: #474796">guid</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">guidUnicodeConverterCmdSet</span><span style="color: #1060b6">"</span>
                      <span style="color: #474796">id</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">UnicodeMenuGroup</span><span style="color: #1060b6">"</span> <span style="color: #474796">priority</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">0x0300</span><span style="color: #1060b6">"</span><span style="color: #0057a6">&gt;</span>
        <span style="color: #0057a6">&lt;</span><span style="color: #333385">Parent</span> <span style="color: #474796">guid</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">guidSHLMainMenu</span><span style="color: #1060b6">"</span> <span style="color: #474796">id</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">IDM_VS_CTXT_CODEWIN</span><span style="color: #1060b6">"</span><span style="color: #0057a6">/&gt;</span>
    <span style="color: #0057a6">&lt;/</span><span style="color: #333385">CommandPlacement</span><span style="color: #0057a6">&gt;</span>
    <span style="color: #0057a6">&lt;</span><span style="color: #333385">CommandPlacement</span> <span style="color: #474796">guid</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">guidUnicodeConverterCmdSet</span><span style="color: #1060b6">"</span>
                      <span style="color: #474796">id</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">UnicodeMenuGroup</span><span style="color: #1060b6">"</span> <span style="color: #474796">priority</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">0x0300</span><span style="color: #1060b6">"</span><span style="color: #0057a6">&gt;</span>
        <span style="color: #0057a6">&lt;</span><span style="color: #333385">Parent</span> <span style="color: #474796">guid</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">CMDSETID_HtmEdGrp</span><span style="color: #1060b6">"</span> <span style="color: #474796">id</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">IDMX_HTM_SOURCE_HTML</span><span style="color: #1060b6">"</span><span style="color: #0057a6">/&gt;</span>
    <span style="color: #0057a6">&lt;/</span><span style="color: #333385">CommandPlacement</span><span style="color: #0057a6">&gt;</span>
    <span style="color: #0057a6">&lt;</span><span style="color: #333385">CommandPlacement</span> <span style="color: #474796">guid</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">guidUnicodeConverterCmdSet</span><span style="color: #1060b6">"</span>
                      <span style="color: #474796">id</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">UnicodeMenuGroup</span><span style="color: #1060b6">"</span> <span style="color: #474796">priority</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">0x0300</span><span style="color: #1060b6">"</span><span style="color: #0057a6">&gt;</span>
        <span style="color: #0057a6">&lt;</span><span style="color: #333385">Parent</span> <span style="color: #474796">guid</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">CMDSETID_HtmEdGrp</span><span style="color: #1060b6">"</span> <span style="color: #474796">id</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">IDMX_HTM_SOURCE_SCRIPT</span><span style="color: #1060b6">"</span><span style="color: #0057a6">/&gt;</span>
    <span style="color: #0057a6">&lt;/</span><span style="color: #333385">CommandPlacement</span><span style="color: #0057a6">&gt;</span>
    <span style="color: #0057a6">&lt;</span><span style="color: #333385">CommandPlacement</span> <span style="color: #474796">guid</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">guidUnicodeConverterCmdSet</span><span style="color: #1060b6">"</span>
                      <span style="color: #474796">id</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">UnicodeMenuGroup</span><span style="color: #1060b6">"</span> <span style="color: #474796">priority</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">0x0300</span><span style="color: #1060b6">"</span><span style="color: #0057a6">&gt;</span>
        <span style="color: #0057a6">&lt;</span><span style="color: #333385">Parent</span> <span style="color: #474796">guid</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">CMDSETID_HtmEdGrp</span><span style="color: #1060b6">"</span> <span style="color: #474796">id</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">IDMX_HTM_SOURCE_ASPX</span><span style="color: #1060b6">"</span><span style="color: #0057a6">/&gt;</span>
    <span style="color: #0057a6">&lt;/</span><span style="color: #333385">CommandPlacement</span><span style="color: #0057a6">&gt;</span>
    <span style="color: #0057a6">&lt;</span><span style="color: #333385">CommandPlacement</span> <span style="color: #474796">guid</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">guidUnicodeConverterCmdSet</span><span style="color: #1060b6">"</span>
                      <span style="color: #474796">id</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">UnicodeMenuGroup</span><span style="color: #1060b6">"</span> <span style="color: #474796">priority</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">0x0300</span><span style="color: #1060b6">"</span><span style="color: #0057a6">&gt;</span>
        <span style="color: #0057a6">&lt;</span><span style="color: #333385">Parent</span> <span style="color: #474796">guid</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">CMDSETID_HtmEdGrp</span><span style="color: #1060b6">"</span> <span style="color: #474796">id</span><span style="color: #308080">=</span><span style="color: #1060b6">"</span><span style="color: #1060b6">IDMX_HTM_SOURCE_ASAX</span><span style="color: #1060b6">"</span><span style="color: #0057a6">/&gt;</span>
    <span style="color: #0057a6">&lt;/</span><span style="color: #333385">CommandPlacement</span><span style="color: #0057a6">&gt;</span>
<span style="color: #0057a6">&lt;/</span><span style="color: #333385">CommandPlacements</span><span style="color: #0057a6">&gt;</span></pre>
<p>And thats it! I didn&rsquo;t made a step-by-step tutorial how to make a new extension for Visual Studio. There are a lot of other articles and examples around for that. But I thought I show you what problems I was confronted with and how I finally circumnavigate them so that you can learn from my researches.</p>
<p>Last but not least you can take a look at my sourcecode. I&rsquo;m publishing it under the MS-PL so you are free to use and change it for your purposes but of course it would be great if you could give me a hint in that case. All feedback is greatly appreciated, so leave me a comment or write a message via email, facebook, twitter, &hellip; whatever you like <img class="wlEmoticon wlEmoticon-winkingsmile" style="border-bottom-style: none; border-right-style: none; border-top-style: none; border-left-style: none" src="http://anheledirwp.blob.core.windows.net/wordpress/2010/08/wlEmoticonwinkingsmile.png" alt="Winking smile" /></p>