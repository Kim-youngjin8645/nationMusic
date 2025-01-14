# nationMusic
코드및영상
https://drive.google.com/file/d/1vfgSheEmIyNIQiqcFXlL5xmd6tkkldTp/view?usp=drive_link

package NationMusic;

import java.io.*;
import java.util.*;


public enum Continent {/* 6. 대륙 열거형: 각 대륙을 정의한 열거형*/
    Asia, Europe, Africa, Americas, Oceania; /* 6개의 대륙을 정의*/
}


interface NationalMusic {/* 3. 국가 음악 정보를 출력하는 인터페이스*/
    void displayInfo();  /* 국가 정보 출력 메서드*/
}

// 2. 국가 추상 클래스 상속: 국가의 기본 정보와 국가별 음악을 관리
abstract class Country {
    private String name;        // 국가 이름
    private Continent continent; // 국가의 대륙

    public Country(String name, Continent continent) {
        this.name = name;        // 국가 이름 초기화
        this.continent = continent; // 대륙 초기화
    }

    public String getName() {
        // 국가 이름 반환
        return name;
    }

    public Continent getContinent() {
        // 국가의 대륙 반환
        return continent;
    }

    public abstract void showNationalMusic();
    // 각 국가별 음악 정보 출력용 추상 메서드
}

class SpecificCountry extends Country implements NationalMusic {// 구체 클래스: 특정 국가
    private String music; // 국가의 대표 음악

    public SpecificCountry(String name, Continent continent, String music) {
        super(name, continent);  // 부모 클래스 초기화
        this.music = music;      // 대표 음악 초기화
    }

    @Override
    public void displayInfo() {
        // 국가 정보 출력
        System.out.println("국가: " + getName() + ", 대륙: " + getContinent());
    }

    @Override
    public void showNationalMusic() {
        // 국가의 대표 음악 출력
        System.out.println(getName() + "의 국가 음악: " + music);
    }

    public String getMusic() {
        return music;
    }
}

// 4. 예외 처리 클래스
class CountryNotFoundException extends Exception {
    public CountryNotFoundException(String message) {
        super(message); // 예외 메시지 초기화
    }
}

public class NationalMusicProgram {
    public static void main(String[] args) {
        // 7. 컬렉션 프레임워크: 국가 데이터를 저장하는 리스트
        List<SpecificCountry> countries = new ArrayList<>(); // 국가 데이터 저장용 리스트
        Scanner scanner = new Scanner(System.in);

        // 초기 국가 데이터 추가
        countries.add(new SpecificCountry("대한민국", Continent.Asia, "아리랑"));
        countries.add(new SpecificCountry("프랑스", Continent.Europe, "라 마르세예"));
        countries.add(new SpecificCountry("미국", Continent.Americas, "스타 스팽글"));
        countries.add(new SpecificCountry("일본", Continent.Asia, "사쿠라"));

        String fileName = "national_music.txt"; // 파일 저장용 이름

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
                    String name = scanner.nextLine();
                    System.out.print("대륙 (Asia, Europe, Americas, Africa, Oceania): ");
                    String continentInput = scanner.nextLine();
                    Continent continent;
                    try {
                        continent = Continent.valueOf(continentInput); // 6. 열거형을 사용한 대륙 입력
                    } catch (IllegalArgumentException e) {
                        System.out.println("유효하지 않은 대륙명입니다.");
                        break;
                    }
                    System.out.print("대표 음악: ");
                    String music = scanner.nextLine();
                    countries.add(new SpecificCountry(name, continent, music)); // 국가 추가
                    System.out.println("국가 데이터가 추가되었습니다.");
                    break;

                case 2: // 데이터 삭제
                    System.out.print("삭제할 국가명: ");
                    String removeName = scanner.nextLine();
                    boolean removed = countries.removeIf(c -> c.getName().equals(removeName)); // 7. 컬렉션을 이용한 데이터 삭제
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
                        SpecificCountry foundCountry = findCountryByName(countries, searchName); // 5. 다형성을 이용한 검색
                        foundCountry.displayInfo(); // 국가 정보 출력
                        foundCountry.showNationalMusic(); // 대표 음악 출력
                    } catch (CountryNotFoundException e) {
                        System.err.println(e.getMessage()); // 예외 처리
                    }
                    break;

                case 4: // 파일 저장
                    saveToFile(countries, fileName); // 8. 파일 입출력 사용
                    break;

                case 5: // 파일 읽기
                    readFromFile(fileName); // 8. 파일 입출력 사용
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

    // 8. 파일 저장
    private static void saveToFile(List<SpecificCountry> countries, String fileName) {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(fileName))) {
            for (SpecificCountry country : countries) {
                writer.write(country.getName() + "," + country.getContinent() + "," + country.getMusic());
                writer.newLine(); // 파일에 한 줄씩 저장
            }
            System.out.println("데이터가 파일에 저장되었습니다: " + fileName);
        } catch (IOException e) {
            System.err.println("파일 저장 중 오류 발생: " + e.getMessage());
        }
    }

    // 파일 읽기
    private static void readFromFile(String fileName) {
        try (BufferedReader reader = new BufferedReader(new FileReader(fileName))) {
            System.out.println("파일에서 읽은 데이터:");
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line); // 파일에서 한 줄씩 읽기
            }
        } catch (IOException e) {
            System.err.println("파일 읽기 중 오류 발생: " + e.getMessage());
        }
    }

    // 5. 다형성: 특정 국가 검색
    private static SpecificCountry findCountryByName(List<SpecificCountry> countries, String name) throws CountryNotFoundException {
        for (SpecificCountry country : countries) {
            if (country.getName().equals(name)) {
                return country;
            }
        }
        throw new CountryNotFoundException("국가를 찾을 수 없습니다: " + name); // 예외 발생
    }
}
