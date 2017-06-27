# Maven Minify 적용
## pom.xml
```xml
<build>
    <finalName>IotManager</finalName>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.0</version>
            <configuration>
                <source>1.7</source>
                <target>1.7</target>
            </configuration>
        </plugin>
        <plugin>
            <groupId>com.samaxes.maven</groupId>
            <artifactId>minify-maven-plugin</artifactId>
            <version>1.7.4</version>
            <executions>
                <execution>
                    <id>default-minify</id>
                    <configuration>
                        <!-- <cssSourceDir>css</cssSourceDir>
                            <cssTargetDir>css.min</cssTargetDir> -->
                        <cssSourceIncludes>
                            <cssSourceInclude>**/*.css</cssSourceInclude>
                        </cssSourceIncludes>
                        <!-- <cssSourceExcludes>
                            <cssSourceExclude>**/*.min.css</cssSourceExclude>
                        </cssSourceExcludes> -->
                        <!-- <jsSourceDir>js</jsSourceDir>
                            <jsTargetDir>js.min</jsTargetDir> -->
                        <jsSourceIncludes>
                            <jsSourceInclude>**/*.js</jsSourceInclude>
                            <jsSourceInclude>**/*.html</jsSourceInclude>
                        </jsSourceIncludes>
                        <jsSourceExcludes>
                            <jsSourceExclude>**/keybinding-vim.js</jsSourceExclude>
                            <jsSourceExclude>**/worker-coffee.js</jsSourceExclude>
                            <jsSourceExclude>**/worker-html.js</jsSourceExclude>
                            <jsSourceExclude>**/worker-javascript.js</jsSourceExclude>
                            <jsSourceExclude>**/worker-xquery.js</jsSourceExclude>
                        </jsSourceExcludes>
                        <skipMerge>true</skipMerge>
                        <nosuffix>true</nosuffix>
                        <debug>true</debug>
                    </configuration>
                    <goals>
                        <goal>minify</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>2.2</version>
            <configuration>
                <warSourceDirectory>src/main/webapp</warSourceDirectory>
                <warSourceExcludes>**/*.css, **/*.js, **/*.html</warSourceExcludes>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-eclipse-plugin</artifactId>
            <version>2.9</version>
        </plugin>
    </plugins>
</build>
```

# Ant Minify 적용
## build.xml
```xml
......
    <property name="css.dir" value="${WebContent.dir}/springmvc/css" />
    <property name="js.dir" value="${WebContent.dir}/springmvc/js" />
    <property name="css.build.dir" value="${build.dir}/${css.dir}" />
    <property name="js.build.dir" value="${build.dir}/${js.dir}" />
    
    <target name="web.resource.build" description="WEB자원 복사">
        <echo># WEB자원 복사</echo>
        <delete dir="${build.dir}/${WebContent.dir}" />
        <copy todir="${build.dir}/${WebContent.dir}" overwrite="true" includeemptydirs="true">
            <fileset dir="${WebContent.dir}" excludes="**/.svn/, **/*.css, **/*.js, WEB-INF/**" />
        </copy>
    </target>
    
    <target name="css.minify" depends="web.resource.build" description="CSS 파일 minify 처리">
        <echo># CSS 파일 Minifying ...</echo>
        <apply executable="java" parallel="false">
            <fileset dir="${css.dir}" includes="**/*.css" excludes="**/.svn/, **/minify/" />
            <arg line="-jar" />
            <arg path="${WebContent.dir}/WEB-INF/lib/yuicompressor-2.4.7.jar" />
            <srcfile />
            <arg line="--charset UTF-8 -o" />
            <mapper type="glob" from="*.css" to="${css.build.dir}/*.css" />
            <targetfile />
        </apply>
    </target>
    
    <target name="js.minify" description="JS 파일 minify 처리">
        <echo># JS 파일 Minifying ...</echo>
        <apply executable="java" parallel="false">
            <fileset dir="${js.dir}" includes="**/*.js" />
            <arg line="-jar" />
            <arg path="${WebContent.dir}/WEB-INF/lib/yuicompressor-2.4.7.jar" />
            <srcfile />
            <arg line="--charset UTF-8 --type js -o" />
            <mapper type="glob" from="*.js" to="${js.build.dir}/*.js" />
            <targetfile />
        </apply>
    </target>

    <target name="war" depends="css.minify, js.minify" description="Creates the WAR file">
        <mkdir dir="${build.dir}" />
        .......
        <war destfile="${build.dir}/${WEB_NAME}.war" webxml="${WebContent.dir}/WEB-INF/web.xml" manifest="${build.dir}/MANIFEST.MF">
            <fileset dir="${build.dir}/${WebContent.dir}">
                <patternset>
                    <include name="**/*.*" />
                </patternset>
            </fileset>
            <webinf dir="${WebContent.dir}/WEB-INF/">
        ......
......
```