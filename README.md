# nationMusic
코드및영상
https://drive.google.com/file/d/1vfgSheEmIyNIQiqcFXlL5xmd6tkkldTp/view?usp=drive_link

package NationMusic;
import java.io.*;
import java.util.*;

public enum Continent {
	Asia, Europe, Africa, Americas, Oceania//6.대륙 열거형
 
}
interface NationalMusic{
//3.인터페이스 국가음악정보들
	void displayInfo();
}
abstract class Country{
//국가 추상클래스
	private String name;//이름
	private Continent continent;//대륙이름
	
	public Country(String name, Continent continent) {
		this.name=name;//국가이름저장
		this.continent=continent;//대륙저장
	}
	public String getName() {
 //국가이름반환
		return name;
	}
	public Continent getContinent() {
 //국가 대륙반환
		return continent;
	}
	public abstract void showNationalMusic();
 //추상메서드
}
 //구체 클래스: 특정국가 Country를 상속받아 국가이름,대륙,음악정보초기화.
class SpecificCountry extends Country implements NationalMusic{//5.다형성
	private String music;
	
	public SpecificCountry(String name, Continent continent, String music) {
		super(name, continent);
		this.music=music;	
	}
	@Override//3.인터페이스method의 다형성 
	public void displayInfo() {
		System.out.println("국가: "+getName()+", 대륙"+getContinent());
	}
	@Override//추상method구현부
	public void showNationalMusic() {
		System.out.println(getName()+"의 국가"+music);
	}
	public String getMusic() {
		return music;
	}
}
class CountryNotFoundException extends Exception{//4.예외처리
	public CountryNotFoundException(String message) {//예외 메시지초기화
		super(message);
	}
}
package NationMusic;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;


public class NationalMusicProgram{
	public static void main(String[] args) {
		List<SpecificCountry> countries= new ArrayList<>();//7.국가데이터들 저장,List컬렉션
		Scanner scanner = new Scanner(System.in);
		//국가 데이터 추가 이름,대륙,국가순서로 데이터삽입(초기데이터)
		countries.add(new SpecificCountry("대한민국",Continent.Asia,"아리랑"));
		countries.add(new SpecificCountry("프랑스", Continent.Europe,"라 마르세예"));
		countries.add(new SpecificCountry("미국", Continent.Americas, "스타 스팽글"));
		countries.add(new SpecificCountry("일본",Continent.Asia,"사쿠라"));
		
		//8.파일 저장 및 읽기
		String fileName="national_music.txt";
		while (true) {
            System.out.println("\n=== 국가 음악 관리 프로그램 ===");
            System.out.println("1. 국가 데이터 추가");
            System.out.println("2. 국가 데이터 삭제");
            System.out.println("3. 국가 데이터 검색");
            System.out.println("4. 파일 저장");
            System.out.println("5. 파일 읽기");
            System.out.println("6. 종료");
            System.out.print("선택: ");
            int choice = scanner.nextInt();
            scanner.nextLine(); // 개행 문자 제거

            switch (choice) {
                case 1: // 데이터 추가
                    System.out.print("국가명: ");
                    String name = scanner.nextLine();//입력받기
                    System.out.print("대륙 (Asia, Europe, Americas, Africa, Oceania): ");
                    String continentInput = scanner.nextLine();
                    Continent continent;
                    try {
                        continent = Continent.valueOf(continentInput);
                    } catch (IllegalArgumentException e) {//예외처리
                        System.out.println("유효하지 않은 대륙명입니다.");
                        break;
                    }
                    System.out.print("대표 음악: ");
                    String music = scanner.nextLine();
                    countries.add(new SpecificCountry(name, continent, music));
                    System.out.println("국가 데이터가 추가되었습니다.");
                    break;
                case 2: // 데이터 삭제
                    System.out.print("삭제할 국가명: ");
                    String removeName = scanner.nextLine();
                    boolean removed = countries.removeIf(c -> c.getName().equals(removeName));
                    if (removed) {
                        System.out.println("국가 데이터가 삭제되었습니다.");
                    } else {
                        System.out.println("삭제할 국가를 찾을 수 없습니다.");
                    }
                    break;

                case 3: // 데이터 검색
                    System.out.print("검색할 국가명: ");
                    String searchName = scanner.nextLine();
                    try {
                        SpecificCountry foundCountry = findCountryByName(countries, searchName);
                        foundCountry.displayInfo(); // 국가 정보 출력
                        foundCountry.showNationalMusic(); // 대표 음악 출력
                    } catch (CountryNotFoundException e) {
                        System.err.println(e.getMessage());
                    }
                    break;

                case 4: // 파일 저장
                    saveToFile(countries, fileName);
                    break;

                case 5: // 파일 읽기
                    readFromFile(fileName);
                    break;

                case 6: // 종료
                    System.out.println("프로그램을 종료합니다.");
                    scanner.close();
                    return;

                default:
                    System.out.println("잘못된 선택입니다. 다시 입력해주세요.");
            }
        }	
	}
	// 8.파일 저장
    private static void saveToFile(List<SpecificCountry> countries, String fileName) {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(fileName))) {
            for (SpecificCountry country : countries) {
                writer.write(country.getName() + "," + country.getContinent() + "," + country.getMusic());
                writer.newLine();//파일한줄씩저장
            }
            System.out.println("데이터가 파일에 저장되었습니다: " + fileName);
        } catch (IOException e) {//저장오류메시지.
            System.err.println("파일 저장 중 오류 발생: " + e.getMessage());
        }
    }
	// 파일 읽기
    private static void readFromFile(String fileName) {
        try (BufferedReader reader = new BufferedReader(new FileReader(fileName))) {
            System.out.println("파일에서 읽은 데이터:");
            String line;
            while ((line = reader.readLine()) != null) {//한줄씩읽기.
                System.out.println(line);
            }
        } catch (IOException e) {
            System.err.println("파일 읽기 중 오류 발생: " + e.getMessage());
        }
    }

    // 특정 국가 검색
    private static SpecificCountry findCountryByName(List<SpecificCountry> countries, String name) throws CountryNotFoundException {
        for (SpecificCountry country : countries) {
            if (country.getName().equals(name)) {
                return country;
            }
        }
        throw new CountryNotFoundException("국가를 찾을 수 없습니다: " + name);
    }
}
