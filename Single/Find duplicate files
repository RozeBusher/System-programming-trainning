/*Find duplicate files: The system version; given a directory name, it can find all files
with the same content (not necessarily the same name).*/

//Implemented with DFS and md5
#include <bits/stdc++.h>
#include <openssl/md5.h>
#include <fstream>
#include <dirent.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <unordered_map>
#include <unistd.h>
using namespace std;
class DF {
	int bufLen;
	unordered_map<string, string> mp;
public:
	DF(int len) {
		bufLen = len;
	}
	unordered_map<string, vector<string>> *run(DIR *dp) {
		unordered_map<string, vector<string>> *rst;
		recur(rst, dp);
		return rst;
	}
	bool isFile(char *path) {
		struct stat ps;
		stat(path, &ps);
		return S_ISREG(ps.st_mode);
	}
	bool cmpF(const string& p1, const string& p2) {
		ifstream f1(p1, ifstream::binary | ifstream::ate);
		ifstream f2(p2, ifstream::binary | ifstream::ate);
		if (f1.fail() || f2.fail()) return 0;
		if (f1.tellg() != f2.tellg()) return 0;
		f1.seekg(0, ifstream::beg);
		f2.seekg(0, ifstream::beg);
		return equal(istreambuf_iterator<char>(f1.rdbuf()), istreambuf_iterator<char>(), istreambuf_iterator<char>(f2.rdbuf()));
	}
	void recur(unordered_map<string, vector<string>> *rst, DIR *dp) {
		for (struct dirent *obj = NULL; obj = readdir(dp);) {
			if (!obj) {
				printf("Invalid directory!\n");
				exit(1);
			}
			if (isFile(obj->d_name)) {
				MD5_CTX ctx;
				MD5_Init(&ctx);
				FILE *fp = fopen(obj->d_name, "r");
				char buf[bufLen];
				int bytes;
				while ((bytes = fread(buf, 1, bufLen, fp)) != 0)
					MD5_Update(&ctx, buf, bytes);
				unsigned char hshbuf[MD5_DIGEST_LENGTH] = {};
				MD5_Final(hshbuf, &ctx);
				string md5str(reinterpret_cast<char*>(hshbuf)), fn(obj->d_name);
				if (!mp.count(md5str))
					mp[md5str] = fn;
				else if (cmpF(fn, mp.at(md5str))) {
					if (!rst->count(md5str))
						rst->at(md5str).push_back(mp.at(md5str));
					rst->at(md5str).push_back(fn);
				}
				fclose(fp);
			}
			else {
				DIR *tdp = opendir(obj->d_name);
				if (!tdp) {
					printf("Invalid element!\n");
					exit(1);
				}
				recur(rst, tdp);
				closedir(tdp);
			}
		}
	}
};
int main() {
	DIR *dir = opendir("DIR_NAME");
	if (!dir) exit(1);
	DF df(1024);
	unordered_map<string, vector<string>> *rst = df.run(dir);
	closedir(dir);
	return 0;
}
