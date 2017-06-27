# [GlueMobile] Server-Side Minify 적용방법

### htmlcompressor (HTML minify) [download](https://code.google.com/archive/p/htmlcompressor/)
### YUI compressor (CSS, JS minify) [download](http://yui.github.io/yuicompressor/)
### Project Package (Glue Java Project)
![](http://www.solutionpot.co.kr/editor/multiupload/20160705/201607051747416a43e769-c037-453a-adcd-a6e9d1f207e2.PNG)
### build.xml (ant)
```xml
<?xml version="1.0"?>
<project name="glue-ant-minify" default="Copy_WAR">
    <property file="build.properties" />
    <!-- CSS폴더 지정 -->
    <property name="css.dir" value="${WebContent.dir}/css" />
    <!-- JS폴더 지정 -->
    <property name="js.dir" value="${WebContent.dir}/js" />

    <!-- build/WebContents폴더를 삭제 후에 minify할 파일들을 제외하고 build/WebContents폴더에 복사한다. -->
    <target name="static.resource.copy" description="Static resource delete &amp; copy">
        <echo># Static resource delete &amp; copy</echo>
        <delete dir="${build.dir}/${WebContent.dir}" />
        <copy todir="${build.dir}/${WebContent.dir}" overwrite="true" includeemptydirs="true">
            <fileset dir="${WebContent.dir}" excludes="**/*.html, **/*.css, **/*.js, **/.svn/, WEB-INF/**" />
        </copy>
    </target>

    <!--
        WebContents폴더에 HTML 파일들을 minify하고 build/WebContents에 위치 시킨다.
        참고 : https://code.google.com/archive/p/htmlcompressor/
    -->
    <target name="html.minify" depends="static.resource.copy" description="HTML file minify">
        <echo># HTML file minifying ...</echo>
        <apply executable="java" parallel="false">
            <fileset dir="${WebContent.dir}" includes="**/*.html" excludes="**/.svn/" />
            <arg value="-jar" />
            <arg path="${WebContent.dir}/WEB-INF/lib/htmlcompressor-1.5.3.jar" />
            <arg line="--charset UTF-8 --type html" />
            <arg value="--preserve-comments" />
            <srcfile />
            <arg value="-o" />
            <mapper type="glob" from="*.html" to="${build.dir}/${WebContent.dir}/*.html" />
            <targetfile />
        </apply>
    </target>

    <!--
        WebContents폴더에 CSS 파일들을 minify하고 build/WebContents에 위치 시킨다.
        참고 : http://yui.github.io/yuicompressor/
        이슈 : 최신 버전인 2.4.8은 FileNotFoundException 발생하여 하위 버전 사용
    -->
    <target name="css.minify" depends="html.minify" description="CSS file minify">
        <echo># CSS file minifying ...</echo>
        <apply executable="java" parallel="false">
            <fileset dir="${css.dir}" includes="**/*.css" excludes="**/.svn/" />
            <arg line="-jar" />
            <arg path="${WebContent.dir}/WEB-INF/lib/yuicompressor-2.4.7.jar" />
            <srcfile />
            <arg line="--charset UTF-8 -o" />
            <mapper type="glob" from="*.css" to="${build.dir}/${css.dir}/*.css" />
            <targetfile />
        </apply>
    </target>

    <!-- WebContents폴더에 JS 파일들을 minify하고 build/WebContents에 위치 시킨다. -->
    <target name="js.minify" depends="css.minify" description="JS file minify">
        <echo># JS file minifying ...</echo>
        <apply executable="java" parallel="false">
            <fileset dir="${js.dir}" includes="**/*.js" excludes="**/.svn/" />
            <arg line="-jar" />
            <arg path="${WebContent.dir}/WEB-INF/lib/yuicompressor-2.4.7.jar" />
            <srcfile />
            <arg line="--charset UTF-8 --type js -o" />
            <mapper type="glob" from="*.js" to="${build.dir}/${js.dir}/*.js" />
            <targetfile />
        </apply>
    </target>

    <target name="war" depends="js.minify" description="Creates the WAR file">
        <manifest file="${build.dir}/MANIFEST.MF">
            <section name="POSCO ICT Glue Sample Project">
                <attribute name="Implementation-Version" value="${project_version}" />
            </section>
        </manifest>
        <war destfile="${build.dir}/${WEB_NAME}.war" webxml="${WebContent.dir}/WEB-INF/web.xml" manifest="${build.dir}/MANIFEST.MF">
            <!-- build/WebContents폴더에 모든 파일을 war에 포함 시킨다. -->
            <fileset dir="${build.dir}/${WebContent.dir}">
                <patternset>
                    <include name="**/*.*" />
                </patternset>
            </fileset>
            <webinf dir="${WebContent.dir}/WEB-INF/">
                <patternset>
                    <include name="*.*" />
                </patternset>
            </webinf>
            <classes dir="${class.dir}">
                <patternset>
                    <include name="**/*.*" />
                    <exclude name="layout/*.xsd" />
                </patternset>
            </classes>
            <lib dir="${GlueSDK.dir}/lib/gluelib">
                <include name="glue-schema*.jar" />
                <include name="glue-core*.jar" />
                <include name="license*.jar" />
            </lib>
            <lib dir="${GlueSDK.dir}/lib/gluestd">
                <include name="bcprov*.jar" />
                <include name="xmlbeans*.jar" />
                <include name="jaxp-api*.jar" />
                <include name="spring*.jar" />
                <include name="ehcache*.jar" />
                <include name="slf4j*.jar" />
                <include name="logback*.jar" />
                <include name="jcl*.jar" />
                <include name="commons*.jar" />
                <include name="sqlite*.jar" />
            </lib>
        </war>
    </target>

    <target name="Copy_WAR" depends="war" description="copy">
        <copy todir="${target.WebContent.dir}">
            <fileset dir="${build.dir}">
                <include name="*.war" />
            </fileset>
        </copy>
    </target>
</project>
```