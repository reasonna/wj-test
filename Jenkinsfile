import groovy.json.JsonSlurperClassic 
import groovy.json.JsonOutput
import groovy.json.JsonBuilder

def map = [:]
pipeline {
    agent any   
    environment{ 
        JIRA_CLOUD_CREDENTIALS = credentials('jira-cloud')
        ISSUE_KEY = "${JIRA_TEST_PLAN_KEY}"

    }

    stages {
        stage('Init') {
            steps {
                script {
                    println "!!!!!!!!!!!!!!!!! Init !!!!!!!!!!!!!!!!!" 
                    init(map)   
                    map.jira.auth_user = '$JIRA_CLOUD_CREDENTIALS_USR:$JIRA_CLOUD_CREDENTIALS_PSW'  
                    map.jira.auth = "Basic " + "${JIRA_CLOUD_CREDENTIALS_USR}:${JIRA_CLOUD_CREDENTIALS_PSW}".bytes.encodeBase64()
                }
            }
        }
        stage('Get test plan'){
            steps{
                script{                   
                    println "!!!!!!!!!!!!!!!!! Get test plan !!!!!!!!!!!!!!!!!"
                    map.issue = getJiraIssue(map.jira.base_url, map.jira.auth, ISSUE_KEY)
                    // println "Iseeue = > ${map.issue}"
                    
                }
            }
        }
        stage('Get testcases / Set node'){
            steps{
                script{
                    println "!!!!!!!!!!!!!!!!! Get testcases / Set node !!!!!!!!!!!!!!!!!"
                    def jql = map.issue.fields[map.jira.testCaseJQLField]         
                    def testTablet = map.issue.fields[map.jira.tabletInfoField].value
                    map.agents_ref.each { key, value -> 
                        if(testTablet == key){
                            map.current_node = key 
                            map.current_path = value
                        }
                    }
                    println map.current_node
                    println map.current_path
                }
            }
        }
    }
}

def init (def map){
    map.jira = [:]
    map.jira.site_name = "REASONA"
    map.jira.base_url = "https://reasona.atlassian.net"
    map.jira.tabletInfoField = "customfield_10037"
    map.jira.testCaseJQLField ="customfield_10036"
    
    map.agents_ref = [
        "X500":"C:\\Users\\TB-NTB-223\\CICD\\X500"      //구동가능한 기계
    ]

    map.current_node = null
    map.current_path = null

    map.issue = null

}

def getJiraIssue (String baseURL, String auth, String issueKey){
    def conn = new URL("${baseURL}/rest/api/3/issue/${issueKey}").openConnection()
    conn.setRequestMethod("GET")
    conn.setDoOutput(true)
    conn.setRequestProperty("Content-Type", "application/json;charset=UTF-8")
    conn.addRequestProperty("Authorization", auth)
    def responseCode = conn.getResponseCode()
    def response = conn.getInputStream().getText()
    def result = new JsonSlurperClassic().parseText(response)

    // println responseCode
    // println response
    // println result
    // println conn.getErrorStream()
    // println conn.getResponseMessage()

    return result
}
